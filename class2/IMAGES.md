Images is copy of OS

and this image in aws is called **AMI = Amazon Machine Image**

whats the difference between snapshot

snapshot is the copy of images on the other hand ami is the copy of entire **EC2 instances**

in aws there are 40+ AMI's its called **source images or base images**

both snapshot and ami are stored in aws' s3

we can create EC2 from AMI

IMAGE can be customizable means we can increase root volume size also add additional volume (EBS or ISV) ISV 

AMI are also stored in recycle bin

**If any snapshot has os we can create AMI from that snapshot**

**EC2 Image builder** = we can create images automatically using this concept and those images is called **Golden AMI**

![[Pasted image 20260122230841.png]]


There are not shuch thing called EC2 encrypt only volumes can be encrypt



Images contains data too

**Template**
if we want to all the configuration means all settings we use to launch or in future ec2 we can create template 


	**Template doesnot contain any data but images does**
