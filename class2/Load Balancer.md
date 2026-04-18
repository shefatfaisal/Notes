
4 types of load balancer

- **classic lb**(first) -> http, https, tcp (previous generation old)
- **application lb** -> (modern) default, http and https works on Layer 7
- **network lb** -> (modern) Extreme High performance, works on layer 4, TCP, UDP, TLS, works on Network Level, its the only supports Static ip and 1 static ip per AZ
- **gateway lb** -> 3rd party network virtual security applicance works on **GENEVE - 6081** protocol


maximum used **application lb** because it has **routing Features**

- host-based routing
- path-basad routing
- string parameter routing etc

one big application have multiple sub-domain like google.com
has map.google.com or image.google.com

so problem here if we want to access them we have to create multiple lb associated with that sub-domain but this is not a good idea 

That's why we create a big lb its called **application lb(ALB)**

and then we create multiple **target group** here target is **EC2**

We create some rules  this is called Host-based Routing


So multiple website can be managed by **ALB**

![[Pasted image 20260123201112.png]]



![[Pasted image 20260123201141.png]]