![[Pasted image 20260313002657.png]]![[Pasted image 20260313003031.png]]



# Transferring files jenkins server to remote server using jobs


**Required plugin** = publish over ssh

first we need to pubic and private file
```bash
ssh-keygen
```

then i need to start ssh agent

so my other ec2 instance need the pem file to authenticate

so i need to add .pem file in the **.ssh**

so we cant do this by copy paste 
thats why we need 
```bash
eval $(ssh-agent -s)
```

then i do 

```bash
ssh-add <location of pem file>
ssh-copy-id -i .ssh/id_rsa.pub ec2-user@privateip of ec2
```

by that we can copy my public key into remote instance

we can do without eval

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub -o IdentityFile=~/.ssh/mypemfile.pem ec2-user@172.31.22.24
```


# variables
- user-defined variables
	- global variable
	- local variable
		- scope - spcific job
- jenkin-define variable

For scheduling job we can do either build peridiocally which is nothing but giving cron format time or **Poll SCM** so whenever new commit message will occur a job will execute

![[Pasted image 20260313232048.png]] 

# upload application in the tomcat server

**Required plugin** = Deploy to container

**Tomcat setup**
use the script but change tomcat zip to latest using https://dlcdn.apache.org/tomcat/

# **Sonarqube**:

**Required plugin**
- Maven Integration
- SonarQube Scanner
- Sonar Quality Gates

![[Pasted image 20260314160330.png]]