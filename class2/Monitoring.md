**CloudWatch** is a service which monitors AWS services
it has two types of monitoring 
- Basic monitoring every 5 minutes, default and free
- Detailed monitoring, every 1 min, billable
**Cloudwatch is all about 3 things**
- alarms
- events
- logs
**Cloudwatch can monitor any EC2 services****
Cloud watch need metrics to monitor

There are 4 Host level Matrics its called default
- CPU
- Network
- disk
- status checks
any other metrics except these 4 comes under custom matrics 
here we see memory is not listed here its under custom metrics


These are the metrics for ec2 just like S3 and other service has their own metric 


**Alarm** has 3 states
- In Alarm greater than metrics
- ok - lower than metric
- In-sufficent - if no data is getting from service
**Alarm can also do some actions(==stop, Terminate, Reboot, Recover==)**



**Important**
**Alarm cant so start action**


**Event/Event Bridge**
Event rule -> AWS Service -> EC2 -> Event Categories -> resource -> Route to target

In aws logs can be stored either CloudWatchLogs or S3  

Here we use agent that will be installed in those ec2 so that loges directed to cloudwatch logs. so we have to use IAM ROLE
**TE = EC2**
**Permissions = CW logs**

![[Pasted image 20260213223947.png]]




**CloudTrail** is a service where every activity in aws is tracked for tracking, auditing, Investigation

**CONFIG**: is a service which monitor the changes in AWS


**Security services**: two things
- Encryption in transit (While data is moving) like HTTPS(Certificate) Amazon has own certificate issure like CIA, GoDaddy which name is **ACM(Amazon Certificate Manager)**
- Encryption at rest. We can do that by Encryption keys that service name **KMS(Key Management Service)


**Secret Manager** = to store all secrets

**Aws Backups** to backup all things

Two **Entry points** in **AWS**
- Load Balancer
- Clound Front

Because of so many cyber attacks we can apply a service named **WAF(Web Application Firewall)** and **AWS Shield**() to prevent most common web vulnerabilities attack


**Aws Inspector**: automated vulnerability management service that continuously scans **AWS workloads**

**Trusted Advisor**: provides real time guidance about cost, improve performance, security

**Guard Duty:** Amazon GuardDuty is a threat detection service that continuously monitors your AWS accounts and workloads for malicious activity and delivers detailed security findings for visibility and remediation.


We can have 4 support in aws
- **basic support (free)**
- **developer support**
- **business support**
- **enterprise support**