In AWS we can create group and give permission at the same time whoever in that group or one who will join the group

**But we cannot create group under group**

==we can create maximum of 10 policies for a group or user==

Policy contains **permissions** in json format

but for help **Policy Editor and Policy Generator** we can easily create policies in json

There are **2 types** of policies
- Managed Policy - Created and managed by AWS(Predefined Policies)
- Inline Policy - Created and managed by customer(Customer managed policies)

Every **Aws resources** has **ARN(Amazon Resource Name)**.

So ARN is used for identify policies
![[Pasted image 20260115191445.png]]

There are two types of permissions

- user based permissions
	- EC2 full access
	- s3 readaccess
	- managed policy
- resource level permissions
	- granular level
	- customized permission
	- inline policy



**VERY IMPORTANT**

==IAM user only access aws console and services==

**IAM ROLES  = Temporary access without  Credentials**
there are two things in **IAM ROLE**
- Trusted entity - the service access another service
- Permissions - the service that are accessed by another service

by default two services cant talk with each other but if we create **IAM ROLES** for a service and give permissions then they can communicate

like if we have multiple **EC2** and we want to stop all of them at once. we have to use another service called **lambda** but for that we have to create first IAM ROLE for lambda


here 
TE = lambda
Permissions = EC2


in backened its use **STS(Security Token Service)** service to do the permission stuffs.

![[Pasted image 20260115201106.png]]

Every AWS account has **AWS ACCOUNT ID** of 12 digits but we dont have to remember it 
we can create alias to 

so for third party people if we create iam account to use my one of my company service it would be security risk thats why we should create an **IAM ROLE**. and then a url will generate by default for 1 hour.  There another thing we can do is **Switch Role** where third party user clicks switch Role and put our Account Name and Role name to access my service.

If third party do something illegal we can **Revoke Sessions** or **Delete the IAM ROLE**.

**So role can be attached to services and can be attached user too

![[Pasted image 20260115202317.png]]
So here account got TE to get permissions S3 policy