its unified network all layer 2 has agreed to same layer3 configuration


connected by routers

- Encapsulation: packets
- pupose: Interconnection and management of logical network + **Fragmentation**
- addressing: logical addressing


Classful ip address determines class based of first octet
if first octet starts with
0xxxxxxx then A
10xxxxxx then B
110xxxxx then C
1110xxxx then D
11110xxx then E

**IPV4 -> RFC791

layer 3 sit on the top of layer2

**ethernet(802.3)**
**wifi(802.11)**
**token ring(802.5)**

they above 3 device talk themselves using **layer 2** but whenever they talk with each other they use **layer 3**

some portion in packet

version(4bits) -> 4 or 6
IHL(4bits) -> header length

ToS/Diifserv + ECN -> dont need to know

Total length(16 bits) -> Header + payload

Identification(16bits) + flags(3bits) + fragment offset(13 bits) -> 3 different things 
do **Fragmentation**
Time to live(8bits) #of available hope left. to avoid loop



protocol(8bits)

Header Checksum(16 bits) -> dont need to know

then source ip address 32bits
destination ip address 32 bits

then last two field 
ip optioins - optimal variable length
padding - variable length


except the last two field ipv4 has fix length of 20 bytes packet


mtu = maximum transmission unit 15000 it uses for ethernet frame payload which is packet 

it means this frame can take maximum 15000 bytes payload

but 1500 bytes so small with respect of modern packet thats where fragmentation comes so breaks the packet so that its lower equal than mtu.

**Fragmentation field**: Identification + Flags + Fragmentation Offset

- Identification
	- Used to determin which fragments belong to each other
- Flag
- - indicates how the fragmentation process will be handled
- bit 0 - reserved always 0
- bit 1- if 0, the packet may be fragmented, if 1, do no fragment
- bit 2- if 0 this is the last fragment , 1 if there more





if the packet size is > 1500 ipv4 have to fragment

**Fragmentation math**

1500 ETH MTU - 20 IPV4 header = 1480 bytes of payload max per packet

IPV4 payload length / 1480 = #number of fragments required

for 4440 bytes payload we need = 3 fragments

each fragment have header of ipv4 header so 20 bytes



**id** is same for each of them

| id     | 123      | 123         | 123         |
| ------ | -------- | ----------- | ----------- |
| flags  | 1        | 1           | 0           |
| offset | 0        | 185         | 370         |
|        | 0 - 1479 | 1480 - 2959 | 2960 - 4440 |
difference between routing and switching also ethernet and ipv4 is that 
the source and destination address comes first in ethernet means just send that 

but in routing dest and src comes last means first process all of them then 