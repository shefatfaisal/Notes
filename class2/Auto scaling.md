we can set min, max and Desired capacity means when we first creating we set the DC initially.

so whenever we have a load in our load balancer automatically based on the load scale out and scale in **ASG(Auto Scaling Group)**  would happen.

also we can set the condition like if avg load of cpu > 70% then auto scaling would happen


**3 types of scaling option**
- manual scaling
- Scheduled scaling
- Dynamic scaling

if you want to create **EC2** we need 7 steps
for auto scaling its also need 7 steps

for that we create **launch Template/launch configuration**
we can set all the necessary steps in here


**Load Balancer will monitor the application and cloud watch monitor the EC2**




![[Pasted image 20260123193616.png]]


![[Pasted image 20260123193459.png]]