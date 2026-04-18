pointing time copy of the volumes is called snapshots

We can't create of snapshots of ISV volumes

if we create snapshots first we have to create volume then attach EC2 instances

Snapshot dont have any **AZ**

By default snapshot are **Private**
**Scenario** if two people want to work on the same things on volume they cant share their volume to each other but **they can take snap shot and send them**

**Snapshot Tiers**(billable)
- standard tier(default)
- archive tier
		- we cant create snapshot in this tier we have to restore to standard


**RecycleBin is default**

we can create rules in the recyclebin
and we have two options
- all resource
- tags to sort out what to recycle


**FSR(Fast Snapshot Restore)(billable)**

can restore volume fast


if we want to backup lot of instances we can use **DLCM(Data Life Cycle Manager))**

- we can set rules using tags
- also we can schedule when traffic is low
- also we can set **Retention Period** that means only most recent will be stored rest of them will be deleted


**Important**
Snapshot are **increamental backup** not full backup

means lets say i take snapshot 50 gb and after some time i take another snapshot which is another 50 gb but i only add some files. 
in Increamental Backup concept the second snapshot wont be 50 gb.


**Encryption**

volumes are not **encrypted** by default 

two types of encryption
- in transit using **ACM(Amazon Certificate Manager)**
- at rest use **KMS**

Every service in aws get keys by default

like
- aws/EBS
- aws/se
- aws/rds

so if we want to encrypt we can use above this keys or we can create our own key

By default encryption and decryption is managed by aws

we can use copy option to encrypt

**Snapshot LOCK**

There are two types
- Governance Mode we can decrypt any time
- Compilance Mode we cant decrypt until certain time
![[Pasted image 20260122225152.png]]

