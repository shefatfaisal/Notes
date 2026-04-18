Concept **SSO**

Two scenarios
- Company to AWS = in a company they have on premises so many servers so if an admin manually have to create user account for all of its employee for access individual machine. But if they create **Domain controller** server and creates all the users here which runs **Active Directory** inside and all of the machine is connected to that machine then all of the users can be authenticated by **AD**. So the user is called **Domain Users**. 
  That similiar service can be achieved in AWS by **Directory Services**. This service is completely managed by AWS. The protocol is used is LDAP(LightWeight Directory Access Protocol)

In company we already create users in our AD so we dont want seperate account for aws for employee. So we need connection from aws to the company's AD. So federation is a system that lets users sign in to AWS with their existing identities(like from AD, Google or Facebook) instead of creating seperate AWS user accounts.

**So company needs to Federated/Connection to AWS**


So company AD is providing Identity and AWS is called service provider. So the credentials is passed from the AD to the aws through **SAML(Security Assertion Markup Language)** contains **XML** files.

![[Pasted image 20260115212007.png]]


Another scenario is AWS to AWS

Here Management account gives access multiple services to multiple member accounts

In management account we should create user in Identity Center and that user is called Federated users and assign roles.

Here Identity Center is identity provider. 
![[Pasted image 20260115221716.png]]


**IAM TAGS** = key-value pair and is used for identification purpose and its not IAM specific, it is through out the aws

**Every resources can get maximum of 50 tags**
Tags are optional in **aws**


**IAM Credentials Report**: A report that list all your account users and the status of their various credentials
**IAM Access Advisor**: It shows the service permissions granted to the user and when those were last used.
**IAM Access Analyzer**: It is used to analyze the access of IAM users(unused, risk, etc)
**Organization**: To managed multiple ROOT accounts (Management Account and Member Account)


3 things one policies contain
- Version
- ID
- Statement - (very important)
	- SID
	- Effect = allow/deny
	- actions = start/stop
	- Principle = user/group/role
	- resource = ARN
	- Conditions