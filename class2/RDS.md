we can sign in to the DB services
we can access it through end point

7 engines
- Mysql
- oracle
- Mssql
- postgresql
- mairadb
- aurora - AWS
- ibm db2

RDS is regional

In Database there are lot of read and write operation going so it would get slow that why we can create  another database with read permission. this database have their own access point so application need to access database instead of giving primary one's access point we can mount this to read one which is called **read replica**.

and this read replica we can create different region and different az from the primary one.

So wherenever we edit something in the primary one it will sync to the other one too but this is async means its gonna take some time to replicate

==before creating any read replicas the role for primary is called instance after creating replicas its get primary==

**We can create up to 15 read replicas**

![[Pasted image 20260306211312.png]]



**Multi-AZ**


This is used for High availabilty purposes
there are 3 types we can choose

- 1 primary, 2 replicas(triple the bill)
- 1 primary, 1 replicas (double the bill)
- standalone (no multi az)

its all take care by aws. so here if our primary db instance damages invisible db instance managed by aws gets up but their **end points same.** syncronization is not async means whatever we do in primary will get updated in the other one.

one db instance we have db engine and we can create multiple databases and each database we can create multiple tables 



**Backup**
we called it snapshot(backup of entire **db instance** not **database**)
we can create db instance from snapshot

2 ways we can do backups

- manual 
- automatic using schedule


**Monitoring**
cloudwatch can monitor db instances and gives nice dashboad called **performance insights**

**Storage auto-scalling**
storage can be increase on-fly we can set maximum 64 TB


**Devops Guru** we can monitor how database instance performing

**RDS Proxy**: so when application conncet to databases it does close so rds proxy is feature where it filter out those open connection and close them so database doesnot need to allocate resources for this unnecessary connections

![[Pasted image 20260306214331.png]]