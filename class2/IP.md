- Private ip
- public ip
- elastic ip


**Elastic ip is same as public ip**

**Elastic ip can be transferred from one account to another account**

**but Public ip is dynamic but elastice ip is static**


**AWS gives 5 public ip free but there is a condition you have to use the elastic ip continiously**

**Either you can use only one public or elastic**

within the **vpc** if we want  to communicate with other **EC2** we have to use private ip

if company wants to use **EC2** outside through the internet they use public ip to connect
but if they use vpn then can connect through private

**What is meta data**

**means information about data**

- **Instance MetaData** = Details about instances

you can see meta data about instances in the **Details** section
but if you want to see through **CLI** there is a specific ip for that 
which is http://169.254.169.254/latest/meta-data/

- user-data = bootstrap script
	- user data will only be executed first time

![[Pasted image 20260123220123.png]]