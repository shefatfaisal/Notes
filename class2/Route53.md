 We can use application in aws using **dns name** or **url**


but this **url** is not user friendly

that's why we need to do mapping to nice url

For that AWS have one service called Route53(R53).
This service map nice url to nasty url

So **Route53** is a **DNS** service in AWS

its called 53 because DNS port number is 53

**Route53** have records So Route53 is the first hit service in **AWS**.


This is a **Global service**

## what is Domain

A domain is a human-readable name that identifes a location on the internet.
Domain have a hierarchy, read right to left 


www.myapp.com
 │    │     └── root (invisible dot at the end)
 │    └──────── TLD - Top Level Domain (.com, .org, .net, .io)
 └────────────── subdomain
      myapp   ← this is your registered domain name


## Subdomain

A **subdomain** is a prefix added to your domain to organize different parts of your application. You create them yourself — there's no extra registration needed.

```
myapp.com              ← root domain (your main site)
www.myapp.com          ← "www" subdomain (classic website)
api.myapp.com          ← "api" subdomain (your backend API)
blog.myapp.com         ← "blog" subdomain (your blog)
admin.myapp.com        ← "admin" subdomain (admin panel)
us-east.myapp.com      ← regional subdomain
```

You can create **unlimited subdomains** — they're just DNS records pointing to different resources.


## Hosted Zone

A **hosted zone** is a container in Route 53 that holds all the DNS records for your domain. Think of it like a **folder** or a **settings page** for your domain.

```
Hosted Zone: myapp.com
├── A record        → myapp.com        → 1.2.3.4
├── A record        → www.myapp.com    → 5.6.7.8
├── CNAME record    → blog.myapp.com   → myblog.wordpress.com
├── MX record       → myapp.com        → mail server
└── TXT record      → myapp.com        → "v=spf1 ..."
```

There are two types:

|Type|Purpose|
|---|---|
|**Public**|Answers DNS queries from the open internet|
|**Private**|Answers DNS queries only inside your AWS VPC|

**Key things to know:**

- Route 53 automatically creates **NS(Name server)** and **SOA** records when you create a hosted zone
- **NS (Name Server) records** tell the internet _"Route 53 is in charge of this domain"_
- A hosted zone costs **$0.50/month** per zone
## DNS Record

A **record** is a single instruction inside a hosted zone that says _"when someone asks about this name, here's the answer."_

Every record has:

- **Name** — what domain/subdomain it applies to
- **Type** — what kind of answer it gives
- **Value** — the actual answer (IP, another domain, etc.)
- **TTL** — how long (in seconds) browsers/resolvers should cache the answer

### The most important record types:

**A Record** — maps a name to an IPv4 address

```
Name:  myapp.com
Type:  A
Value: 54.239.28.85
TTL:   300
```

**CNAME Record** — maps a name to another domain name

```
Name:  www.myapp.com
Type:  CNAME
Value: myapp.com
TTL:   300
```

⚠️ CNAME **cannot** be used on the root domain (`myapp.com`) — only subdomains.

**Alias Record** — AWS-specific, maps a name to an AWS resource

```
Name:  myapp.com
Type:  A (Alias)
Value: my-alb.us-east-1.elb.amazonaws.com
TTL:   automatic
```

✅ Alias **can** be used on the root domain, and it's free.


## How They All Connect

```
You register ──→ domain (myapp.com)
                    │
                    ▼
Route 53 creates ──→ hosted zone (myapp.com)
                         │
                         ├── records for myapp.com
                         ├── records for www.myapp.com  ← subdomains
                         ├── records for api.myapp.com  ← subdomains
                         └── records for blog.myapp.com ← subdomains
```


# i create multiple hosted zone per domain and which one it choose to route


yes we can but question which one route 53 choose 
the answer is the one in the domain registered

## Visual Example

```
Domain Registrar (e.g., Route 53 Domains / GoDaddy)
└── myapp.com NS records → ns-123, ns-456, ns-789, ns-012
                                        │
                                        │ matches!
                                        ▼
                    ┌─────────────────────────────┐
                    │  Hosted Zone 1 ✅ (ACTIVE)   │
                    │  myapp.com                   │
                    │  NS: ns-123, ns-456...        │
                    │                              │
                    │  A record → 1.2.3.4          │
                    └─────────────────────────────┘

                    ┌─────────────────────────────┐
                    │  Hosted Zone 2 ❌ (IGNORED)  │
                    │  myapp.com                   │
                    │  NS: ns-111, ns-222...        │
                    │                              │
                    │  A record → 9.9.9.9          │
                    └─────────────────────────────┘
```
here we can see in the domain registrar i have NS records ns-123, ns-456, ns-789, ns-012 and it mathcher my first hosted zone 1's NS records so route 53 will use that

## So Why Would Anyone Create Multiple Hosted Zones for the Same Domain?

There are a few **legitimate reasons:**

### 1. Blue/Green DNS Switching

Keep two hosted zones with different configurations. When you want to switch (e.g., point to a new environment), just update the NS records at the registrar to flip traffic.

### 2. Testing / Staging

Build out a new DNS configuration in a second hosted zone and test it before making it live.

### 3. Accidental Duplication ⚠️

The most common reason — people accidentally create a second one. This is a **common mistake** and leads to confusion because changes made to the wrong hosted zone have no effect.

### 4. Private + Public Hosted Zones

This is very common and fully intentional:

```
Public Hosted Zone  → myapp.com (answers internet queries)
Private Hosted Zone → myapp.com (answers queries inside your VPC only)
```

When a resource **inside your VPC** queries `myapp.com`, Route 53 uses the **private** zone. Everyone else gets the **public** zone. This lets you have different behavior internally vs externally.



We can create hosted zone for subdomain too and linked to 
the parent hosted zone


## How to Set It Up

**Step 1:** Create a new hosted zone for the subdomain

```
Create hosted zone: blog.myapp.com
→ Route 53 assigns it NS records:
  ns-111.awsdns-11.com
  ns-222.awsdns-22.net
  ns-333.awsdns-33.org
  ns-444.awsdns-44.co.uk
```

**Step 2:** Add an NS record in the **parent hosted zone** pointing to the subdomain's NS records

```
In myapp.com hosted zone, add:
  Name:  blog.myapp.com
  Type:  NS
  Value: ns-111.awsdns-11.com
         ns-222.awsdns-22.net
         ns-333.awsdns-33.org
         ns-444.awsdns-44.co.uk
```

**Step 3:** Add your actual DNS records inside the subdomain hosted zone

```
In blog.myapp.com hosted zone, add:
  Name:  blog.myapp.com
  Type:  A
  Value: 5.6.7.8
```



TYPE NS means where resolver should look into the DNS


For parent when resolver asks whet is blog.myapp.com parent 1 hosted zone said idk
ask this NS   
		Value: ns-111.awsdns-11.com
         ns-222.awsdns-22.net
         ns-333.awsdns-33.org
         ns-444.awsdns-44.co.uk
 
 and it is same as child hosted zone ns which is create automatically when we create child hosted zone this is called **NS Delegation**


![[Pasted image 20260309180613.png]]
![[Pasted image 20260309180933.png]]
![[Pasted image 20260309181430.png]]
![[Pasted image 20260309181735.png]]
![[Pasted image 20260309181822.png]]