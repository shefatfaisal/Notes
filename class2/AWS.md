- AWS means amazon web services
- AWS is a group of services
- we have to access those services through AMC (Amazon Management Console)


There 3 kinds of service

**Infracture as a service customer has responsibility of os, data and application**



**First service :-EC2(Elastic Compute Cloud)** is a service from AWS where we can launch instances and AWS has no access inside instances. provides IAAS.
- can instances vm/instances


**ElasticBeanStalk**: PAAS(Platform as a service) 

- Easy and quick deployment of application in AWS


**Elasticity**: Increasing and decreasing the number of servers based on the load is called elasticity

**Elasticity is a short term**
ex: Black friday deal instead of increasing the capacity of instances we launch more instances and after that we delete them

Elasticity can be achieved by **auto scaling**

Auto scaling = scale out (Increasing or adding )and scale in (decreasing or removing)

Elasticity is also called as **horizontal scaling**

**We can auto scaling web server (nginx, apache) or application server(tomcat) but we cant auto scale database server because it's too big** 

![[Pasted image 20260108182103.png]]

here these server in green box is called **auto scaling group** and we have to use same **capacity** for those servers in ASG(Auto Scaling Server)


**Scalability**: Increasing the capacity of the server is called scalability

Scalability = scale up and scale down

**Scalability is Long term**

Scalability is also called as **Vertical Scaling**
one instance is 2 gb ram another instance is 4 gb ram There are different instances in AWS

That's why scalability can be achieved by changing the **instance type**.

Instance type = Combination of CPU + Memory

Scalability is for individual group
![[Pasted image 20260108183338.png]]

**High Availability**: The period of time the service is available to the customer is called HA

**Downtime**: The period of time the service is not available to the customer is called downtime.


**LB is doing the monitoring** 
LB is doing the health checks for **application** not **EC2**

**Some keyWords**:

**Redudancy**: Duplicate/Keeping same application in diff servers

**Monitoring**: LB will check if application is working or not using health checks

**Failover**: If one server goes down, other server will take request


If we have these 3 in a server we can called that server **high availability.**

we can apply auto scaling on top of High availability.

0 downtime is achieved by **auto scaling** also called as **Fault Tolerance**.

![[Pasted image 20260108192926.png]]


LB can distribute the traffic to multiple EC2 instances across AZ's

LB is specific to **Region**
EC2 is specific to **AZ**

