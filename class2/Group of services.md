**AWS services is a group of services**

Services can be either global or regional

Most of the services are regional 
EC2 is regional

Load balancer doesnot go down because it's not a server its a service provided by AWS.
AND its called **ELB(Elastic Load Balancer)**.

We can login load balancer like server we can access it through DNS Name(URL) and it's auto generated

Load balancer is specific to **Region** not **AZ**

We already know that ElasticBeanStalk is **paas**(Platform as a service) does not have any control over server.

but in **ElasticBeanStalk** we have full control on EC2 instances by Beanstalk
But, it is not recommended to control, as Beanstalk is handling EC2 instances


For one EC2 instances we have to follow **7** steps

**LightSail**: If you want setup and installed lightsail instances which has everything installed (Wordpress, Nodejs, Nginx, Cpanel) etc. But you cant auto scale LightSail instances


**Lambda**: Lambda is serverless and you can run the code without the server


We can create functions in lambda and we can write those functions in any languages like c, c++, python, go, java

Lambda is used for automation just like if any event is created then its triggers lambda function 

EVENT ->
- Launch
- Stop
- Start
- Reboot
- Terminate

Lambda is **regional** service by **AWS**


**All the events are stored in Event Bridge**

![[Pasted image 20260219001447.png]]
This limits is important for certificate question