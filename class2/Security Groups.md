Firewall = which stops unauthorized access to the network


**SecurityGroup** = which stops unauthorized access to the EC2 instances . SG is used to secure the EC2 instance acts like a firewall

**2 rules**
- inbound rule
- outbound rule


by default inbound rule are **denied** and outbound rule are **Allowed**


By default we have default one security group 

Who can access our website?
- Custom
- Anywhere
- MyIP

if you want to give some rule in the inbound you dont have to do in the outbound this concept is called **STATEFUL**

**SECURITY GROUPs are STATEFUL**

If you allow inbound, you must allow on outbound also this is called stateless

**NACL(Network Access Control List)** is stateless

Subnet is isolated network in vpc

1 AZ can have multiple subnets

1 subnet is associated to only 1 AZ

**NACL** is applied in **Subnet Level**
**Security Group** is applied in EC2 level

1 subnet = 1 nacl

1 nacl = can have multiple subnets

![[Pasted image 20260123185824.png]]

![[Pasted image 20260123185736.png]]

## What is a NACL?

NACL sits at the **subnet boundary** — every packet entering or leaving a subnet passes through it.

```
Internet
    │
[IGW]
    │
[NACL]  ← checks every packet IN and OUT of subnet
    │
[Subnet]
    ├── EC2-1
    ├── EC2-2
    └── EC2-3
```

Think of it like an **ACL(Access Control List) on a router interface** — if you've ever done Cisco/networking, it's exactly that. Inbound and outbound rules on the subnet's "interface."

---

## Stateless — This is the Most Important Part

Your home router's NAT is **stateful** — when your laptop sends a request out, the router remembers the session, so the reply automatically comes back to your laptop. You don't need to explicitly allow the return traffic.

NACL is **stateless** — it has **no memory of sessions.** Every packet is judged independently.

So if you allow inbound HTTP (port 80), you **must also explicitly allow outbound** for the return traffic to leave — otherwise the reply packet gets dropped at the subnet boundary on the way out.

```
Client → (inbound port 80) → EC2        ✅ allowed by inbound rule
EC2 → (outbound ephemeral port) → Client  ❌ DROPPED if no outbound rule
```

---

## Ephemeral Ports — Why This Catches People Off Guard

When your laptop connects to a web server on port 80, the **server replies back to a random high port** on your laptop (e.g., 32768–60999). These are called **ephemeral ports.**

So in your NACL outbound rules, you must allow:

```
Outbound → TCP → 1024-65535 → 0.0.0.0/0   ✅
```

Otherwise return traffic gets silently dropped.

---

## Rule Evaluation — Numbered Order

Unlike Security Groups (which evaluate ALL rules), NACL evaluates rules **top to bottom, first match wins.**

```
Rule 100  →  Allow  TCP  0.0.0.0/0  port 80
Rule 200  →  Allow  TCP  0.0.0.0/0  port 443
Rule 300  →  Deny   TCP  1.2.3.4    port 80    ← TOO LATE, rule 100 already matched
Rule *    →  Deny   ALL  ALL                   ← implicit deny at the end
```

So **order matters a lot.** If you want to block a specific IP, that deny rule must come **before** the broad allow rule.

---

## NACL vs Security Group — Clear Distinction

||NACL|Security Group|
|---|---|---|
|Where|Subnet boundary|EC2 instance (ENI)|
|State|Stateless|Stateful|
|Rules|Allow + Deny|Allow only|
|Evaluation|Numbered order, first match|All rules evaluated together|
|Return traffic|Must explicitly allow|Automatic|
|Default|Allow all (default NACL)|Deny all inbound|

---

## Traffic Flow With Both Layers

```
Inbound packet from internet → EC2:

[IGW] → NACL inbound check → [Subnet] → SG inbound check → [EC2]
                ↓ deny?                        ↓ deny?
             dropped                         dropped


Outbound reply from EC2 → internet:

[EC2] → SG outbound check → [Subnet] → NACL outbound check → [IGW]
              ↓ deny?                          ↓ deny?
           dropped                           dropped
```

Packet has to **pass through both** — NACL at subnet level, SG at instance level.

---

## When to Actually Use NACL

Since Security Groups handle most filtering, NACLs are mainly used for:

- **Blocking a specific IP range** — SGs can't deny, only allow. If you want to block a bad actor's IP, NACL is the only way.
- **Subnet-wide rules** — one NACL rule blocks traffic for ALL EC2s in that subnet at once, instead of updating every SG.
- **Extra defense layer** — defense in depth. Even if someone misconfigures an SG, NACL is still there.

---

## Default NACL vs Custom NACL

||Default NACL|Custom NACL|
|---|---|---|
|Created with|VPC automatically|You create it|
|Default inbound|Allow ALL|Deny ALL|
|Default outbound|Allow ALL|Deny ALL|
|Use case|Dev/testing|Production|

Default NACL allows everything — so by default, subnets rely only on Security Groups for filtering. In production you create custom NACLs and lock it down explicitly.



# interview question


when creating a vpc 3 things by default is created

- Security group
- route table
- NACL





VPC
├── [NACL is here — at the edge of the subnet]
│
└── Subnet
      ├── EC2-1
      ├── EC2-2
      └── EC2-3


## The Simple Rule

||Allowed|
|---|---|
|One AZ → many subnets|✅|
|One subnet → one AZ|✅ (hard rule)|
|One subnet → many AZs|❌ never|