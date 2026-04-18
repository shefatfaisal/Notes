![[Pasted image 20260123220457.png]]

so there is a situation that we cant give ip addresses of load balancer to the customer because its dynamic.
To resolve this aws invented **Global Accelerator** where customer can have 2 static ip which is **(Anycast ip)** and then talk to GA and GA then talk to edge location to connect our LB.

GA is completely managed by aws and it is billable


why two because it is a fundamental high availability concept that is one thing is go down other can active