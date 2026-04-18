IEEE splits L2 into two parts: 
- Upper sub-layer it is LLC(Logical Link Control) 
	- interface with upper layers(L3)
	- LLC is a software that talks will L3 
- lower sub-layer it is MAC(Media Access Control)
	- Addressing, framing, Media access
	- how to converts into bits, radiowave
So they create two header layer 2 mac(IEEE802.3) and layer 2 LLC(IEEE 802.2)mac address is a physical address

it is a flat address space which means nothing about a mac address describes the network it is on  its just an identifier for the network card on the device


IETF -> Request For comment(RFC)(Internet Engineering task force)
IEEE -> 802 standards (Institute for Electrical and electronics Engineering)

A header is ordered collection of field -value pairs 

- a definition or fromat
- de facto - industry accepted
- de jure - approved / recognized by standards body
- ![[Pasted image 20260302142529.png]]
![[Pasted image 20260302142755.png]]
![[Pasted image 20260302143127.png]]
![[Pasted image 20260302143139.png]]



DTE (Data Terminal Equipment)
DCE(DATA Communications Equipment)
Media - utp, stp, optical fiber, coax


Transmission methods 3
- simplex
- half duplex
- full duplex
Ethernet is covered by ieee 802.3 standard

Ethernet features
- mac adressing
- machester encoding
- csma/cd
- ethernet frames

for pc 1 2 tx and 3 6 rx for hub and switch 1 2 rx and 3 6 tx
![[Pasted image 20260302153411.png]]

![[Pasted image 20260302153853.png]]
![[Pasted image 20260302154225.png]]

First byte least significant bit


IEEE splits L2 into two parts: 
- Upper sub-layer it is LLC(Logical Link Control) 
	- interface with upper layers(L3)
	- LLC is a software that talks will L3 
- lower sub-layer it is MAC(Media Access Control)
	- Addressing, framing, Media access
	- how to converts into bits, radiowave
So they create two header layer 2 mac(IEEE802.3) and layer 2 LLC(IEEE 802.2)

CSMA/CD (Carrier sense multiple access / collision detection)
![[Pasted image 20260302164007.png]]
	![[Pasted image 20260302164029.png]]
	![[Pasted image 20260302165639.png]]



802.11 -> wifi

![[Pasted image 20260302211242.png]]


ipv4 packet

version - 4 bits
IHL(internet Header length) - 4 bits -> 5 words 32 = 20 bytes
TOS(8 bits)
length - 16 bits

identification - 16 bits
flag - 3bits
- first bit reserved
- second bit
	- 0 = can be fragmented
	- 1 = donot fragment
- third bit
	- 0 = last fragment
	- 1 = more fragment
offset - 13 bits


ttl = 8bits
protocol = 8bits
header checksum = 16bits


source = 32

destination = 32



![[Pasted image 20260302214259.png]]![[Pasted image 20260302214618.png]]
![[Pasted image 20260302214658.png]]![[Pasted image 20260302214709.png]]


Subnetting

network address = routing prefix
Broadcast address =  directed broadcast

![[Pasted image 20260302224133.png]]

for host houting if we ping to a node in the same subnet means  pc 1 to 2 then 

![[Pasted image 20260302231113.png]]


![[Pasted image 20260302231137.png]]
see the row number 5 column 3 and 4 we see that gateway and interface if pc1 ip means that thats ip is me so i dont need to use the router to send the packet i can do that on my own