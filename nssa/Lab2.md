**Ethernet**


**Layer 1 is the tramission into topologies**
Ethernet is collection of thing

it stays on Data link/physical layer

IEEE802.3 standard code for ethernet

IEEE took over ethernet they did not invent it

**Layer1 Ethernet**

every layer has its own version of nic

so ethernet has its own

8 pins in total
8 seperate wires

its a serial things

the wire are twisted in pairs

This cable is called **UTP(Unshielded Twisted pair cable)**

Each pair has its associated color and own job

the name of the port is 8P8C not rj45

Tranmission methods between a and b
- Simplex: only one direction **a -> b** only
- Half-Duplex: either **a -> b** or **b -> a** not both at the same time (Original ethernet) if send at a time collision happens, data corrupted
- Full-Duplex: **a -> b** (Modern Ethernet) No collisions
			**and**
			**a <- b**



Topologies


- Bus
	- Physical: Coax Cable with devices attached via 
	- logical: half duplex connections
- Star
	- Physical
	- Logical Full duplex connections
- Ring
	- physical: Interconnection in a ring shape
	- logical:Token-based Communication


**Cabling**
- straight-through have straight through
- cross-over
	-  1 goes to 3 and 2 goes to 6 on both end rest of them normal



**Data link** has to do things
- purpose: Inter networking (Networking between devices on the same network)
- addressing: physical addressing -> mac addressing
- encapsulatioin: Frames   [FrameHeader]  [payload]  [Freame trailer] PDU


Ethernet standards
- Ethernet two
- Ethernet 802.3

**Ethernet 2 Structure**
both them have **Destination addrss(6byte)-> Source address(6byte) -> type(2byte)** here type means what is the next header or protocol like it is **ipv4 or ipv6** this is the most basic and important header syntax this whole thing is called **layer two header**

also **ethernet two** has trailer its called FCS 4 Bytes = it uses cryptographic algorithm called CRC to check is there any temper with data

also we have its called **layer 1 header** (Preamble) 8 bytes



**802.3 structure
Now we talk about Ethernet 802.3 structure


which is very similiar
**Layer 1 header**:
we have same preamble **7byte** and **1 byte** of **SOF(start-of-delimiter)**  
SOF is very specific which is `10101011` and indicates that the next bit is the first bit of destination of mac address

**Layer 2 header**

the sequence are same **dest mac** -> **Source MAC** -> **Length**(2 bytes)
 Type is very important because its the most important thing that differntiate between ethernet 2 and 802.3 structure.
**Length is literally the size of the payload means the rest of the message**

IEEE splits L2 into two parts: 
- Upper sub-layer it is LLC(Logical Link Control) 
	- interface with upper layers(L3)
	- LLC is a software that talks will L3 
- lower sub-layer it is MAC(Media Access Control)
	- Addressing, framing, Media access
	- how to converts into bits, radiowave
So they create two header layer 2 mac(IEEE802.3) and layer 2 LLC(IEEE 802.2)

in wireshark they title with nicely with header
**Length doesnot include padding so sometimes it will be less than 46 because padding is not added**

After that we have LLC field which sits between length and the payload

In LLC we have
- DSAP (1byte)
- SSAP (1byte)
- control unit (1byte)


then **Data then FCS**

The reason is layer 2 has two many things to work with layer 1 and 3 


	![[241_2255_01_ethernet.pdf]]



