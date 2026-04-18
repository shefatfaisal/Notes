


Connection Method
- Unicast
- multicast
- broadcast


There's lot of unicast or multicast address

but only one broadcast address

steps done in switch

- check FCS(FCS/CRC)
- Filter of Flood
- add source to the **SAT(Source Address Table)** aka L2 forwarding table also called as **CAM**
**Filter**:
in **SAT** switch found destination MAC using **Linear Search**


**Flood**
Frame is sent out all other interfaces 
to BUM destination

BUM = Broadcast Unknown-unicast Multicast

![[Pasted image 20260201122653.png]]
![[Pasted image 20260201123418.png]]

![[Pasted image 20260201123439.png]]


