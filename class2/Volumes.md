There are two types of volumes
- EBS volumes
	- persistent storage/permanent
	- 16TB
	- EBS is billable
- Instance Store Volumes(ISV)
	- Non persistent sotrage/Temporary volumes
	- stop and start = data is lost
	- free 
	- ephimeral storage
	- if you stop ISV volume data is lost but if you reboot its not lost? weird 
	- if we do ISV AWS dont give us any stop option only **terminate**

If we stop and start a machine it goes from one harddisk to another.
but for reboot machine remains in the same hardisk
when we do ISV it takes some space from harddisk  from aws infrastructure.
thats why data gets lost.

Types of EBS volumes
- general purpose(gp2, gp3) -> normal good performance
	- ssd
- provisined IOPS(Input Output Per Second)(io1, io2) -> extreme high performance
	- ssd
- ThroughPUT(st1) -> frequently but cheaper price
	- hdd
- cold(sc1)->not frequently cheaper price
	- hdd
- Magnetic(standard)->slow
	- hdd



HDD is slower than SSD

**WHich one IOPS is configurable?**
ans: IO1, IO2, GP3


So root volume have to be one of this type of volume

**For windows is GP2**
**Linux which is GP3**
**IOPS ratio** for gp2 = 1:3 means 1GB for 3IOPS


**Root volume only support gp2, gp3, io1, io2, standard except st1 and sc1**

**Additonal = all types**

**So one value cannot be attached to multiple instances at the same time**

but io1 and io2 can be multi attached and we can attach to 16 instances


**Question:** if we terminate EC2 instance root valume will be deleted by default?
Ans: Yes because **delete on termination enabled**


**Question:** if we terminate EC2 instance additional valume will be deleted by default?
Ans: NO because **delete on termination disabled**

![[Pasted image 20260122175817.png]]


There are 3 types **Status check** done by aws
- instance status check - hardware
- system status check - software, ip
- EBS Volume status check - volume attachment

if you want to launch a machine all 3 of them should be passed


Recently aws brings brought another feature which is called **auto recover** any time checks failed occures it will auto recover

Q:**Which one is faster?**
EBS or ISV

Ans: ISV because it is local but EBS is centralized in EBS we combile two services of aws E2 and volumes.

Instance has 6 states

- Launch/start = pending -> Running
- Stop = Stopping -> Stopped
- Terminate = Shuttingdown -> Terminated

**EC2 Hibernate**

Only EBS volume can be hibernate and it have to be **encrypted** we cant hibernate any instance more than 60 days
![[Pasted image 20260122190453.png]]
