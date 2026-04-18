This is the combination on on-premises and aws
**scenario**: suppose in a company everyone storing their file in storage so its gonna full soon and storage manager will need to buy the storage again again so aws came up with a solution where aws storage services can be mounted in the on-premises devices so that we can use aws storage services without creating like s3 bucket, object etc.


Storage gateway 3 aws storage services
- s3
- ebs
- glacier

here we need something as a middle man to communicate with aws and on-premises thats why we need agent and here we call this **storage gateway appliance** which will be installed in the on-premises in a vm.

![[Pasted image 20260306203514.png]]


question: which aws service is a combination of public and private means hybrid
ans: storage gateway

**Architecture**
![[Pasted image 20260306203807.png]]
