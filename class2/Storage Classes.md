- Standard Frequently Access
	- Frequently access
	- default storage class
	- regular purpose
	- no retrival charges
- Standard Infrequently Access - IA
	- not regular / infrequently
	- once a month
	- Retrival chages apply
	- min object size = 128KB
	- Min duration = 30 days
- Reduced Redundancy Storage(RSS)
	- Frequently
	- not critical
	- no retrival charges
	- not recommend
- One Zone IA
	- infrequently data not critical
	- Retrival charges apply
	- min object size = 128KB
	- Min duration = 30 days
- Intelligent Tier
	- Unknown access patterns
	- Min duration = 30 days
- S3 Glacier
	- infrequently access
	- Archive purpose
	- we create Vault:archives
	- 1 archive = 40TB
	- retrival charges apply
	- We have to wait to retrieve data
		- Expedited = 1to 5 mins
		- standard = 3 to 5 hours
		- bulk = 5 to 12 hours
	- Min duration = 90 days
- Deep Glacier
	- if you want to access data once in a few years
	- Min duration = 180 days

We can move object from one storage class to another automatically 
which is called **life cycle management** we create **Life cycle rules** there
![[Pasted image 20260226014140.png]]

 **LCM Rules**

LCM rules can be created for current versions and non-current versions
two action 
- Transition
- Expiration
Once froward is possible backward is not possible

if we want to analyze logs who is accessing my bucket we can use athena

if we want access bucket log the feature we need to enable is server access log


if we want to access object level log/events we can use cloud trails

4 types of events
- management event (by default for bucket level event)
- data event (for access object level event)
- insight event(suspicious)
- network event(network logs)

**Object lock** if enabled no one can delete, edit

![[Pasted image 20260226014203.png]]

**CORS(Cross origin resource sharing)** its mean sharing resources between two bucket by default its disabled


**CRR(Cross Regional Replication)** Anything upload in one region s3 to another region s3
Versioning must be enabled replicate based  upon prefixes.
We need to create CRR rule

SRR(Same Regional Replication) same region

![[Pasted image 20260226014907.png]]