Network consists of 
* Host
* packet switches
* routers
* communication links(fiber, copper, radio, satellite)
we can characterise network
* Home network
* enterprise network

> "Routers comes with firewall or net"

Internet is network of network 
network protocols is like (Http, DNS, TCP, UDP, IP)

there are two major paradigms for end hosts to communicate to each other
* client-server paradigm 
	* server have to always on and static ip
	* client doenot require to always on and vary ip
* peer to peer paradigm
	* not always on server. anyone can join ex:  P2P file sharing (Bittorrent)



# Internet protocol Stack
consists of 5 layers 
* Application (defines for highest level of application protocol to communicate end-host with each other) ex: HTTPS, FTP
* Transport (Process-process data transfer) ex: TCP, UDP
* Network (routing services provides best route to pass your data to the server)
* link(Data transfer between neighbouring network elements)
	* Ethernet(802,4), 802.11(Wifi)
* physical(then physical layer is responsible for converting my requests into bits and bytes and transferring these bytes correctry over different kinds of communitcaiton link such as electromagnetic, light pulses)
# Encapsulation and decapsulation
The process of extracting an IP datagram from the payload of an Ethernet frame and passing it to the network layer is called ==de-encapsulation==.
**Physical layer**:  is to understand each communication link transfers your data. It suffices to know each network elements on the network including endhosts and packet switches has a pair of transmitter and receiver device. when it wants to send single bit it converts it into a specific shape based on the communication medium. 
Ex: 

### wired communication Medium ###
* Ethernet
	* very cheap 
	* most common 
	* two insulated copper wires, up to 1gbps(cat 5)
* coaxial calbe:
	* two concentric copper conductors
	* 100's mbps per channel
	* common
* Fiber optic cable
	*  glass fible carrying light pulses, each pulses a bit
	* costly
	* high speed 
### wireless communication medium(Carry the bits freely in the air)###
* wifi
* radio
**link** : link layer is responsible for transferring datagram from one node to physically adjacent node over a link 
* link layer implemented if network interface card(NIC) or on a chip
ex: ethernet protocol or 802.11 wifi protocol are both implemented by a hardware on a different chip or card and attach to the motherboard

ok in a shared broadcast channel it is possible one reciving node get two transmissions this is called collision domain 
thats why we have to introduce **multiple access protocol** 
* Distributed algorithm that determines  how nodes share channel ex: determine when node can transmit
most common protocol is csma/cd (Carrier Sense Multiple Access with Collision Detection)
carrier sense means that it allows a node when another node is transmitting. multiple access means we have multiple nodes in node sharing and finallyy collision detection means that a node can detect when another node at transmitting at the same time.

**Mac address**
when we talk about NIC its actually installed in our hardware. and there is physical location in each of the NIC adapter 
Mac is 48 bit mac-address(for most LANs) burned in NIC ROM
		
Ethernet Switch:
	Switch is link-layer device. it solves collison. it sends and recieve frams at the same time. modern switches are full- duplex(no interference) thers no need for csma/cd protocol for ethernet-switched lans 
		switches are transparent means hosts unaware of presence of swiches
		no need for switch to configure to accomade a new hosts
		how does switch know about connected hosts and how to reach them 
		ans is **self learning**. switch is kind of a plug in-out device no information were installed in switch about the hosts . switch makes  a table take copy about the hosts 
		a well known attacked is called switch poisoning the attacker overwhelms the switch with many frames.
		**Face** : switch table capacity is limited
		attacker sends many frames with bogus source MAC adresses
			no room for legitimate  MAC addressses
			then an attacker runs a sniffer on compromised host
			wireshark packet sniffer tool that can be used to monitore all in and outgoing traffic of a host.
**Layer 3**: network layer is responsible for transporting a segment  it recieves from the above layer from sending to receiving host example router. 
	Router examines header fields in all IP output ports to transfer datagrams to another router or hop. The next router is determined by routing algorithm.
	2 key functions:
		* routing function
			* determine best route datagram take from source to destination
		*  Forwarding function 
			*  moving datagram router's input link to the correct router's output link
	ip protocol are only implemented in network layer and they are 32 bits long
	**interface** connection between host/router and physical link
		router's typically have multiple interfaces
		host typically has one or two interfaces(wired ethernet, wireless 802.11)
		ipv4 address is represented using the decimal numbering system
		every 8 bits represented as 0 - 255 decimal number 4 decimal number then splited by . sign
		so interface on lan has unique 48-bit mac address
		and has a locally unique 32-bt ip address
	analogy:
		mac addresss: ssn
		ip address: postal address
		mac address is portable
		but ip isnot portable because if we switch network  ip address will highly likely change
	**ip spoofing** : injecting datagrams into the internet with a fake source ip address
**subnets**
	device interfaces that can physically reach each other without passing through an interventing router
**ip addresses have structure** 
	**subnet part(assigned by enterprise by the arin(American Registry for Internet numbers) through their isp):** devices in same subnet have common high order bits means first 24 bit are same 
	**host part(assigned by network admins):**  last low order bits (last 8) all bits 0 also .0(as decimal ) called network address  this address refers to the entire network.
	and ip address with host part of all bit 1 means (.255 as decimal) is called the broadcast address those two address should be assigned to any host or router's interface on the network
**Network address format**: ends with forward slash followed by a number x. this x is called subnet mask. and is equal to the number of bits in the subnet portion.
ex: 222.1.1.0/24x
there are many types network address class A, B, C by in terms of different size of subnet size and host size  subnetting mask donot holds individual host address rather than a subnet
		
	
