
EBS means **Elastic Block Storage**

In EC2 instances we can get a default harddisk that is called volume or EBS Volume

so Hard disk = Volume = EBS Volume

in EC2 the default volume contains **OS** and that volume we called **root volume**.

There are 2 types of volumes in EC2

- Root volume
- additional volume - contains data


So we can attach multiple volumes

There are also two types of os 
- server side os (windows server 2024, 2025)
- client side os(windows 8, 9, 10, 11)

In EC2 we can create only **server side os**

So we can launch windows machine or linux machine
By default windows server get 30gb and linux 8/10 gb


for maximum we can get volume up to **16TB**

**You cannot attach  a volume to multiple EC2 at the same time*****

We can **increase** the volume size but we cant **decrease** the volume size we have to delete it first then we can.


Root volume always attach or mount in **/dev/sda1 or /dev/xvda** location
for additonal /dev/sdb, e, f etc

**volumes cannot be shared** means we can detach from EC2 and attach to another

![[Pasted image 20260112222745.png]]



For sharing storage we need to introduce another service called **EFS(Elastic File System)** it uses **NFS** protocol to share files across multiple instances

- **EFS only works in linux instance**
- **EFS is maintained by AWS
- **EFS is File based storage**
- **EFS is unlimited storage**
- **EFS can be mounted at multiple instances accross AZ and its regional**
- **EFS can be replicate to  another region here replicate means duplicate data from one region to another**

For **windows** we got another service called **FSx**

- **FSx works on SMB Protocol - Server Message Block



So for big amount of data we cant upload or move online For that we need offline way

There's is another service that does this is called **Snow Family**
There are 3 devices
- SnowCone - 8 TB
- SnowEdge - 100 TB
- SnowMobile - PB's

and then they move this data to s3

so snowFamily transfers physical data using devices




If we dont use data frequently we can use another service called **Glacier** 
its used for **Archive Purpose**

similar to **s3** Glacier contains **Vaults** is container of archives
archives = .zip file

One zip file can be up to 40 TB
we can have unlimited number of archives
1000 Vaults


**Storage Gateway**
so in company they have their own storage but if we want to have aws storage service we need Storage Gateway service to implement aws service in the local storage

This is called hybrid cloud solution


![[Pasted image 20260114234318.png]]