**On-Demand Instances**

- Fixed price(Hourly)
- Pay for what you have used
- pay per hour
- No commitment
- No upfront payment
- No Predictable usage

**Reserved Instances**
- Long term Commitment
- 1 or 3 years
- upfront payment(Full, partial)
- Upto 72% discount on hourly price
- Predictable usage
There are two types for RI
- Standard RI: 72%
- Convertable RI: 66%
Also there is RI marketplace if you dont need the service you can sell

**SPOT Instances**
- bidding
- auctioning
- Huge capacity for cheaper
- 90% discount
- can loose the EC2 instances

==Tenancy==

EC2 instances created in the host machine

For different 2 customer they can have EC2 instances in the same host machine.

This situation is called their host machine' **Tenancy** is shared. But in AWS we can do have where we have **Tenancy** **dedicated** that means we have specific invidual machine where we can launch EC2. so we can choose the location of our EC2 by that.


So for that types of user there are pricing model

**Dedicated Hosts**

book entire physical machine, control on instance palcements

**Dedicated Instances**
No other customer will share the hardware, no control on placements of **EC2**

**Capacity Reservation**

in AZ aws is gonna reserve capacity for the company 

No time commitement

**Savings Plan**
same as RI, it has more flexibillity
