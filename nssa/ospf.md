> NSSA 241 · Week 11 · Professor Willis

---

## Table of Contents

1. [What is OSPF](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#1-what-is-ospf)
2. [LSDB — Link State Database](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#2-lsdb--link-state-database)
3. [Dijkstra & Why It's Expensive](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#3-dijkstras-algorithm--why-its-expensive)
4. [Flat vs Hierarchical](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#4-flat-vs-hierarchical)
5. [Area Rules](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#5-the-three-unbreakable-area-rules)
6. [Router Types](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#6-router-types)
7. [LSA Types](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#7-lsa-types--the-data-records)
8. [Message Types](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#8-ospf-message-types--the-delivery-system)
9. [Hello Packet Deep Dive](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#9-hello-packet--deep-dive)
10. [Network Types](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#10-network-types)
11. [OSPF Metric & Cost](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#11-ospf-metric--cost)
12. [Bandwidth Explained](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#12-what-bandwidth-actually-means)
13. [AS & ASN](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#13-autonomous-system--asn)
14. [IP Addressing & Overlaps](https://claude.ai/chat/3e632b11-2315-4fb1-a0c9-b6e1d5aaeaf2#14-ip-addressing--why-overlaps-break-everything)

---

## 1. What is OSPF?

**Open Shortest Path First** — a link-state, hierarchical, interior routing protocol.

- **IGP** (Interior Gateway Protocol) — runs _inside_ one organization's network
- **Link-state** — every router shares info about its own links, building a shared map
- Uses **Dijkstra's algorithm** to calculate best paths from that map

> **Mental model:** Every router is a city that posts its own road map on a public bulletin board. Every other city reads every posted map. Each city then independently calculates the best GPS route to every destination using all the maps combined.

|Term|Meaning|
|---|---|
|IGP|Routing _inside_ one AS — full topology known|
|EGP (BGP)|Routing _between_ ASes — only summaries shared|
|Link-state|Each router knows the full map of its area|
|Distance-vector (RIP)|Each router only knows what its neighbors told it|

---

## 2. LSDB — Link State Database

Every OSPF router maintains its own **LSDB** — a collection of LSAs describing every router and link in the area.

```
Area 1 — every router holds an IDENTICAL copy:

  LSA from R1: "I have interfaces → 10.1.1.1, 10.1.2.1"
  LSA from R2: "I have interfaces → 10.1.1.2, 10.1.3.1"
  LSA from R3: "I have interfaces → 10.1.2.2, 10.1.3.2"

         ↓ fed into Dijkstra's algorithm ↓

  Routing table for R1:
    10.1.3.0/24 → via R2, cost 2
    10.1.2.0/24 → via R3, cost 1
```

**Key facts:**

- LSDB is **per router, per area** — not one shared cloud database
- All routers in the same area must have **identical** copies
- Flooding exists purely to keep all copies synchronized
- One router with a different copy = routing inconsistencies

> **Key insight:** No single LSA describes the whole area. The full picture only emerges when you _combine_ all LSAs together and run Dijkstra on the result.

---

## 3. Dijkstra's Algorithm & Why It's Expensive

Dijkstra finds the shortest path from one node to all others in a graph. OSPF uses it to build the routing table from the LSDB.

**Why it doesn't scale in a flat network:**

```
More routers → larger LSDB → more time to run Dijkstra
                            → more flooding traffic
                            → one link change = everyone recalculates
```

|Scale|Problem|
|---|---|
|10 routers|Fine|
|100 routers|Slow convergence|
|1000 routers|Unusable without areas|

**Solution:** Split the LSDB into areas. Each router only runs Dijkstra on its own area's LSDB. Less data = faster calculations.

---

## 4. Flat vs Hierarchical

### Flat Network — everyone knows everything

```
All 100 routers in one big pool.
One link fails → ALL 100 routers get the update
              → ALL 100 routers re-run Dijkstra
              → entire network stressed simultaneously
```

**Problems:**

- Huge routing tables — every router stores everything
- Flooding hits the whole network — bandwidth wasted
- Slow convergence — update must reach everyone
- No isolation — one flapping link punishes everyone

### Hierarchical Network — OSPF areas

```
         [Area 0 — Backbone]
              ↑       ↑
            ABR      ABR
              ↓       ↓
          [Area 1]  [Area 2]

Link fails in Area 1 → ONLY Area 1 routers recalculate
                     → Area 2 completely unaffected
```

**Benefits:**

- Small LSDBs — routers only know their area in detail
- Flooding contained within area boundaries
- Fast convergence — fewer routers to update
- Failure isolation — problems stay local

> **Mental model:** A city divided into districts. A broken road in downtown only needs to be announced to downtown residents — not the entire country.

||Flat|Hierarchical|
|---|---|---|
|Routing table|Huge|Small (area + summaries)|
|Flood scope|Entire network|Contained to area|
|Failure impact|Affects everyone|Contained locally|
|Scalability|Breaks at scale|Designed to scale|
|Example protocol|RIP|OSPF, IS-IS|

---

## 5. The Three Unbreakable Area Rules

### Rule 1 — Area 0 must always exist

Area 0 is the mandatory central backbone. No OSPF network exists without it. All inter-area traffic must pass through it.

### Rule 2 — Every other area must connect directly to Area 0

Area 1 cannot talk to Area 3 directly. Traffic must go:

```
Area 1 → ABR → Area 0 → ABR → Area 3
```

This creates a mandatory **star topology**. It prevents routing loops and keeps inter-area routing predictable.

```
Legal:                          Illegal:
Area 1 ──ABR──┐                 Area 1 ── Area 2 ── Area 3
Area 2 ──ABR──┤── Area 0        (Area 2 has no path to Area 0)
Area 3 ──ABR──┘
```

### Rule 3 — Area 0 must be contiguous (no splits)

All backbone routers must be physically connected. A split backbone means the two halves cannot sync their LSDBs — routing collapses.

```
ILLEGAL:
[Area 0 chunk A] --- Area 1 --- [Area 0 chunk B]
                     ↑
              Area 1 is not Area 0 — this splits the backbone
```

**Fix if backbone splits:** Virtual link — a tunnel through a non-backbone area to reconnect Area 0. Workaround, not good design.

---

## 6. Router Types

Router type is determined entirely by **where its interfaces sit** — not by configuration.

### Internal Router

- All interfaces in **one non-backbone area**
- Only knows its own area's topology in detail
- Runs Dijkstra once, on its area's LSDB
- Generates: **Type 1 Router LSA** only

> **Analogy:** Employee who only knows the layout of their own department floor.

---

### Backbone Router

- All interfaces in **Area 0 only**
- Keeps the central backbone running
- Handles forwarding between areas
- Generates: **Type 1 Router LSA** for Area 0

> **Analogy:** Highway interchange worker — manages the road everyone else uses.

---

### Area Border Router (ABR) — most critical

- Interfaces in **Area 0 + at least one other area**
- Maintains **two completely separate LSDBs** — one per area, never mixed
- Runs Dijkstra **twice** — once per area independently
- Generates: **Type 3 Summary LSAs** — the only mechanism for routes to cross area boundaries

```
ABR internals:
┌──────────────────────────────────┐
│  LSDB for Area 0  │  LSDB for Area 1  │
│  (never mixed)    │  (never mixed)    │
│         ↓                 ↓           │
│  Reads Area 0     │  Reads Area 1     │
│  networks         │  networks         │
│         ↓                 ↓           │
│  Creates Type 3   │  Creates Type 3   │
│  for Area 1       │  for Area 0       │
└──────────────────────────────────┘
```

**The ABR is a translator, not a mixer.** It reads both databases and generates fresh summaries — the raw LSAs never cross the boundary.

> **Analogy:** Regional manager who summarizes their region to HQ without leaking internal details. HQ gets the summary — not the full internal map.

---

### Autonomous System Boundary Router (ASBR)

- One interface in OSPF AS, one connecting to **external AS** (BGP, EIGRP, RIP, static)
- Redistributes external routes into OSPF
- Generates: **Type 5 External LSAs**

> **Analogy:** Customs officer — everything entering or leaving the country passes through them, translated between systems.

---

## 7. LSA Types — The Data Records

LSAs live **permanently** in the LSDB. Message types deliver them. They are independent systems.

> **Mental model:** LSAs are the books in the library. Message types are the delivery trucks. The truck doesn't care what book is inside. The book doesn't care which truck brought it.

|Type|Name|Who generates|What it describes|Crosses area boundary?|
|---|---|---|---|---|
|**1**|Router LSA|Every router|Its own interfaces only|No|
|**2**|Network LSA|DR only (broadcast)|All routers on that segment|No|
|**3**|Summary LSA|ABR only|Routes from another area (summarized)|Yes — injected by ABR|
|**4**|ASBR Summary|ABR|How to reach an ASBR|Yes|
|**5**|External LSA|ASBR only|Routes outside OSPF entirely|Floods entire AS|

### The most confused pair — Type 2 vs Type 3

```
Area 1 internal:
  Type 1 (R1) + Type 1 (R2) + Type 2 (DR)
                    ↓
              ABR reads all of these
                    ↓
  ABR generates brand new Type 3 → floods into Area 0
  "10.1.0.0/16 reachable via me, cost 15"

The raw Type 1s and Type 2s NEVER enter Area 0.
Type 3 is a fresh document — not a forwarded Type 2.
```

### Why Type 2 exists — the DR problem

On a broadcast segment with 4 routers and no DR:

```
Full adjacencies needed: n(n-1)/2 = 4×3/2 = 6 adjacencies
Each router describes the same segment in its own Type 1
→ 4 competing descriptions of one physical segment
→ Dijkstra sees ambiguity
```

With DR elected:

```
Everyone forms adjacency with DR only → 3 adjacencies
DR generates ONE Type 2 LSA for the whole segment:
  - Subnet mask of segment
  - List of all router IDs attached (R1, R2, R3, R4)
→ One clean unified description
→ Dijkstra has no ambiguity
```

---

## 8. OSPF Message Types — The Delivery System

These are **temporary packets** — sent, acknowledged, done. They do not live in the LSDB.

### Message 1 — Hello

- Sent every **10 seconds** (Ethernet) to multicast `224.0.0.5`
- Three jobs: discover neighbors, negotiate compatibility, maintain heartbeat
- If no Hello arrives within **dead interval (40s default)** → neighbor declared down

### Message 2 — DB Description

- "Here is my LSDB catalog" — LSA **headers only**, no full data
- Like a library sending its card catalog, not the actual books

### Message 3 — LS Request

- "You have these LSAs that I'm missing — send me the full versions"
- Router compares received DB Description against its own LSDB to find gaps

### Message 4 — LS Update

- The **actual full LSA data** — this is what gets stored in the LSDB
- Most important message for building topology knowledge

### Message 5 — LS Acknowledgement

- "I received your LS Update" — uses LSA headers to confirm, not full data again
- OSPF is reliable — every LS Update must be acknowledged or it retransmits

### The full sync flow

```
R1 sends DB Description → "I have: Type 1 from R1, R2, Type 3 from ABR"
R2 checks its own LSDB → "I'm missing the Type 3 from ABR"
R2 sends LS Request    → "Send me that Type 3 LSA"
R1 sends LS Update     → "Here is the full Type 3 LSA data"
R2 stores it in LSDB   → Dijkstra re-runs → routing table updated
R2 sends LS Ack        → "Got it"
```

---

## 9. Hello Packet — Deep Dive

### Must-match fields (gatekeeper)

Mismatch on ANY of these = neighbor silently rejected. No error message. Packet ignored.

|Field|Default (Ethernet)|Why it must match|
|---|---|---|
|Hello interval|10 seconds|If R1 sends every 10s but R2 expects 30s, R2 thinks R1 is dead|
|Dead interval|40 seconds (4× hello)|Both must agree when to declare neighbor gone|
|Area ID|—|Cannot form adjacency across area boundaries|
|Subnet mask|—|Must be on the same network segment|
|Authentication|—|Optional password — both sides must match|

### Informational fields (coordination, no matching needed)

- **Router ID** — who sent this Hello
- **DR / BDR** — designated router election info
- **Priority** — used in DR election (higher = more likely to be DR)
- **Neighbor list** — Router IDs this router has already seen

### The neighbor list — why it's clever

A Hello from R1 doesn't prove R2 can hear R1 (could be one-way link). The neighbor list solves this:

```
Step 1: R1 → Hello → R2    (neighbor list: empty)
Step 2: R2 → Hello → R1    (neighbor list: R1) ← "I see you"
Step 3: R1 sees its own ID → 2-way state confirmed
Step 4: Proceed to DB Description exchange (msgs 2-5)
```

When R1 sees its own Router ID listed in R2's Hello — bidirectionality is confirmed. This state is called **2-way**.

### Neighbor vs Adjacent

- **2-way** = they see each other → neighbors
- **Full** = LSDB fully synchronized → adjacent

Not every neighbor becomes adjacent. On broadcast networks, only the DR and BDR form full adjacency with all routers. Other routers stay at 2-way with each other to avoid traffic explosion.

---

## 10. Network Types

Two separate, independent classifications:

### Classification 1 — Physical type (what hardware)

**Point-to-Point** (e.g. HDLC, leased line)

- Exactly two routers on the link
- No DR election needed — only one possible neighbor
- Generates: Type 1 only

**Broadcast Multi-Access** (e.g. Ethernet switch)

- Many routers on shared medium
- DR elected to prevent adjacency explosion
- DR generates Type 2 Network LSA for the segment

> DR election trigger = broadcast segment with 2+ routers. NOT based on how many routers are in an area. 100 routers all on point-to-point links = zero DR elections.

### Classification 2 — Traffic role

**Transit network**

- Traffic passes _through_ — source and destination are elsewhere
- The network is just a road, not a destination
- Example: backbone link between two routers

**Stub network**

- Dead end — all traffic either originates from or is destined to addresses inside this network
- Nothing just passes through
- Example: office LAN with end-user PCs

### Where network types fit in the OSPF hierarchy

```
OSPF Autonomous System
└── Areas (Area 0, Area 1, Area 2...)
    └── Segments inside each area
        └── Each segment has a network TYPE
```

A single area can contain multiple segment types simultaneously:

```
Area 1:
├── Office LAN (Ethernet)  → Broadcast Multi-Access → needs DR
├── Data center link       → Point-to-Point         → no DR
└── Branch office link     → Point-to-Point         → no DR
```

---

## 11. OSPF Metric & Cost

### What a metric is

A number that quantifies the "distance" to a non-directly-connected network. Lower = preferred.

|Protocol|Metric|
|---|---|
|RIP|Hop count|
|EIGRP|Composite (bandwidth + delay)|
|OSPF|Cost (bandwidth-based on Cisco)|

### What cost is — the RFC definition

RFC 2328 says cost must be: **"a single dimensionless metric"** — just a number. The RFC deliberately leaves the calculation undefined. Each vendor decides how to implement it.

This means Cisco's cost ≠ Juniper's cost for the same link. Mixing vendors in one OSPF network requires explicit cost configuration to avoid surprises.

### Cisco's implementation

```
Cost = Reference Bandwidth / Interface Bandwidth

Reference Bandwidth = 100 Mbps (default, configurable)
Interface Bandwidth = actual speed of that port
```

|Link|Speed|Calculation|Cost|
|---|---|---|---|
|Serial T1|1.5 Mbps|100 / 1.5|**64**|
|Ethernet 10M|10 Mbps|100 / 10|**10**|
|Fast Ethernet|100 Mbps|100 / 100|**1**|
|Gigabit|1,000 Mbps|100 / 1000 = 0.1|**1** ← rounds down|
|10 Gigabit|10,000 Mbps|100 / 10000 = 0.01|**1** ← same as GigE!|

### The modern problem — integer floor

Cost cannot go below 1 (integer). A 100 Mbps and a 10 Gbps link both get cost 1. OSPF treats them identically and will not prefer the faster link.

**Fix:** Raise the reference bandwidth:

```
ip ospf auto-cost reference-bandwidth 10000

Result:
  Fast Ethernet (100M)  → 10000 / 100    = cost 100
  Gigabit (1G)          → 10000 / 1000   = cost 10
  10 Gigabit (10G)      → 10000 / 10000  = cost 1

Now all three are distinct — OSPF correctly prefers faster links.
```

### How path cost is calculated

```
R1 ──(cost 10)── R2 ──(cost 1)── R3 ──(cost 10)── Destination
                                                     ↑
Total path cost = 10 + 1 + 10 = 21

Rule: Only EXIT interface cost counts. Incoming interface is never added.
```

Dijkstra finds the path with the lowest total sum across all hops.

> **Mental model — cost as a budget:** You have 100 coins. A 100 Mbps link spends 1 coin. A 10 Mbps link spends 10 coins. A slow T1 spends 64 coins. OSPF picks the path that spends the fewest coins total to reach the destination.

---

## 12. What Bandwidth Actually Means

### Physical definition

Bandwidth = how much data can flow through a wire **per second**. It is a measure of **capacity** — not speed of individual packets.

```
1 Mbps   link = narrow dirt road  (1 car/sec)
100 Mbps link = 4-lane highway    (100 cars/sec)
1 Gbps   link = 40-lane motorway  (1000 cars/sec)
10 Gbps  link = airport runway    (10,000 cars/sec)
```

All packets travel at the same physical speed (near speed of light). Bandwidth controls how many travel simultaneously.

### What sets the speed

The speed of a link is limited by the **slowest of three things**:

```
1. Cable category
   Cat5e → max 1 Gbps
   Cat3  → max 10 Mbps
   Fiber → 100 Gbps+

2. Port hardware on each end
   A cheap switch port = 100 Mbps even with Cat6 cable

3. Auto-negotiation result
   R1 supports 1 Gbps but R2 only supports 100 Mbps
   → they negotiate down to 100 Mbps
```

### What bandwidth IS vs IS NOT

|Bandwidth IS|Bandwidth IS NOT|
|---|---|
|More data in flight simultaneously|Faster individual packets|
|Large files transfer faster|Lower latency|
|More concurrent connections|More reliability|

### Connection back to OSPF cost

```
Fast link (wide highway) → more capacity → less "expensive" → lower cost
Slow link (dirt road)    → less capacity → more "expensive" → higher cost

OSPF says: "route traffic through the widest highway, not the dirt road"
```

---

## 13. Autonomous System & ASN

### What an AS is

A collection of IP networks under **one organization's control** presenting a single routing policy to the internet. Each AS gets a unique **ASN** from IANA.

```
Google    → AS15169
Comcast   → AS7922
AT&T      → AS7018
Cloudflare → AS13335
```

### Why homes don't need an AS

||Home network|Organization with AS|
|---|---|---|
|Public IPs|1 (from ISP)|Thousands (owned)|
|ISP connections|1|Multiple (redundancy)|
|Routing control|None — ISP decides|Full control via BGP|
|Announces routes?|No|Yes, to entire internet|
|ASN needed?|No|Yes|

> **Mental model:** Home = postal service (ISP) delivers for you, you have no say. Organization with AS = they have their own postal department and negotiate directly with multiple carriers.

### ASN number ranges

- **1–64511** — public ASNs (visible on internet)
- **64512–65534** — private ASNs (like RFC 1918 addresses, internal use only)
- Extended to **32-bit** to accommodate internet growth (up to ~4.2 billion)

### Where OSPF and BGP connect — the ASBR

```
[Your Company AS 65001]       [Internet — other ASes]
┌──────────────────┐
│  OSPF runs here  │──ASBR──BGP──→ AS7018, AS15169...
│  (IGP, internal) │
└──────────────────┘

OSPF = full internal topology known
BGP  = only knows which ASes advertise which prefixes
```

---

## 14. IP Addressing & Why Overlaps Break Everything

### OSPF has no protection against duplicate subnets

If two segments use the same subnet — OSPF blindly advertises both. It has no duplicate detection for subnets (only for Router IDs).

**Same subnet, same area:**

```
Area 1:
├── Segment A: R1 ── R2  (10.1.1.0/24)
└── Segment B: R3 ── R4  (10.1.1.0/24) ← duplicate!

Dijkstra sees two paths to 10.1.1.0/24
→ traffic for 10.1.1.5 might go to Segment A or B randomly
→ wrong device receives it → silent failure
```

**Same subnet, different areas:**

```
Area 1: 10.1.1.0/24
Area 2: 10.1.1.0/24 ← duplicate!

Both ABRs inject Type 3 into Area 0:
  "10.1.1.0/24 via ABR-left"
  "10.1.1.0/24 via ABR-right"

Traffic picks one ABR → ends up in wrong area → wrong destination
```

### The fix — hierarchical IP planning

```
Company block: 10.0.0.0/8
├── Area 0:  10.0.0.0/16
│   ├── Link A: 10.0.1.0/24
│   └── Link B: 10.0.2.0/24
├── Area 1:  10.1.0.0/16
│   ├── Segment A: 10.1.1.0/24
│   └── Segment B: 10.1.2.0/24
└── Area 2:  10.2.0.0/16
    ├── Segment A: 10.2.1.0/24
    └── Segment B: 10.2.2.0/24
```

Each area owns a unique block. Overlap becomes physically impossible.

### Bonus: route summarization only works without overlaps

```
Without hierarchical addressing:
  ABR must advertise 10 individual Type 3 entries

With hierarchical addressing:
  ABR advertises ONE summary:
  "10.1.0.0/16 — everything in Area 1 via me"
  (10 entries collapsed into 1)
```

> **Bottom line:** OSPF assumes you gave it clean, non-overlapping addresses. If you didn't, it faithfully advertises the mess. The network doesn't silently collapse — it _actively_ misbehaves in ways that are very hard to debug.

---

## The One Mental Model for Everything

> Imagine a country (the AS). Inside it are states (areas). Each state has its own detailed road map (LSDB). State officials (internal routers) only know their state's roads. The governor's office (ABR) sits on the state border — it knows both the state roads and the national highway (Area 0), and sends short summaries across borders, never the full state road map. The immigration officer (ASBR) handles everything coming in from foreign countries (external ASes). The federal highway (Area 0) must always exist, always be connected, and all states must connect to it — never to each other directly.
> 
> Every road has a cost. Wider roads (higher bandwidth) cost less. The GPS (Dijkstra) always routes you through the cheapest total path, adding up exit costs at each hop.

---

_NSSA 241 · Professor Willis · Week 11 · OSPF with Multiple Areas_