There are some families listed below
- General instances
- Memory instances
- CPU instances
- Storage instances
- GPU instances
- etc

Instance type = CPU + Memory

- t2.nano
- t2.micro       we will do t3.micro which has 2vcpu, 1GB RAM
- t2.small
- t2.large
- t2.xlarge

we can do scalability by changing the instance types



Anytime Scale up -> Data is not lost

Anytime Scale down -> Data is not lost

**In order to change the instance type you will get downtime because you have to stop the instance first then you can change**


**Scenario** 

if 1000 customer connected in live in one insance which has low cpu then we can't do scale up that instance for that aws has concept which is **credit Specification** or **Burstable** peformance instances where aws gives cpu as credits means aws provide cpu as long is needed but it is billable and only for short time

