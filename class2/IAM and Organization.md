**IAM(Identity and Access Management)** is **global**

There are two types of accout
- Root account
- IAM account


ex: in a company there are only root account and rest of them IAM users

Root account give permission or policies to the IAM users

Root user has full permission

IAM user has limited permissions

IAM means access control on AWS resources to the IAM users


**Organization**: This account is used for control root account .

by default organization is set to disabled

and then all root account is called members account and then they don't have full permissions

by **SCP(Service Control Policy)** we can control root account

**Service Quotas**: Every service has their limits. This service show the limits  of aws resources in each service.


Another service is **Identity Center** where **sso** concept is implemented
if we create users in Identity Center then this user is called **Federated users**
![[Pasted image 20260113184137.png]]



==Deep Dive into IAM==

With IAM user, we can  access AWS Console and Services

Dont share email and password to others for using your services we can give permission or policies to the user

**Also we can share the ROOT account by creating IAM users**


For daily activities we use IAM user

There are 2 ways to access AWS
- Console access (AWS console(GUI)) Browser email/pwd for root and username/pwd for IAM
- programmatical access (CLI, SDK's, Tools) If we want to do AWS command from CLI we need to install **AWSCLI** in win and linux

In CLI we need **Access key** and **Secret key** to authenticate user

**aws configure** is the command by which we can do that

and there are 3 output format we can choose
- table
- xml
- json

We can have maximum 2 set of keys in aws
![[Pasted image 20260115115629.png]]