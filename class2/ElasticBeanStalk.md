![[Pasted image 20260219235023.png]]
There are 3 presets deployment mode 
- single instance
- high availability
	- automic configure load balancer
	- instances
- custom preset
	- customize vpc
	- sg
	- keypair
	- subnet
![[Pasted image 20260219235238.png]]

this is the architecture of elasticBeanstalk so here on top we see application

under the application we can create as many environment we can only swap domain between two environmen in the same application
1 environment has 1 ec2 in single preset

if we want to update out application we have to select deployment policies
there are 4 deployment policies in elastic bean stalk
- all at once
- rolling
- rolling with batches
- immutable

**For single instance preset we have only deployment policy which is all at once**

**immutable**
copy instances with load balancer and upload new version of application in that new environment then move from new environment to the active environment then delete old version ec2

![[Pasted image 20260219235637.png]]



in this picture we see another model blue green deployment model
where we need to change tomcat python so we cant do that on a running environment that's why we create another environment with python and upload new version of application zip file there and then we do **Swap Domain URL** to swap domain URL


There are two types of environment
- web  server 
	- for deployment application
- worker
	- long running processer



