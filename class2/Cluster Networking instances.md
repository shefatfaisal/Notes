
Cluster = group of nodes/EC2 instances/servers

this group is called placement group


We have 3 types placement group

- Cluster PG -> grouping the ec2 instances in the same **AZ** and in the same **rack** high performance but risk of high availability. We can have 20 GBPS of speed if we select this PG
- Spread PG -> grouping EC2 instances accross **AZ**.  **AZ** we can only have 7 **EC2**
- Partition PG -> if you more than 7 go for partition we can have only 7 partition accross **AZ** and each partition we can have numeraous number of EC2 