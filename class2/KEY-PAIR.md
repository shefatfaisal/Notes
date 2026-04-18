If we lauch a EC2 we need 
- ip provided by aws
- username - default name for windows is **Administrator** and for linux is **ec2-user**
- password - for that we need KEY-PAIR to retrive password


**KEY-PAIR** is the combination of public key(AWS) + private key(client)

key-pair file extension is .pem

by default we dont have any private key

one pem file can be attached to multiple EC2
so pem should be create based on the team


but for different ec2 same **pem** give us different password otherwise all ec2 that using same pem file will have same password.

**Pem file cannot be retrived**

windows uses RDP to connect and linux uses ssh


![[Pasted image 20260122232711.png]]