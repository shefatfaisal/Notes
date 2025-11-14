Network consists of 
* Host
* packet switches
* routers
* communication links(fiber, copper, radio, satellite)
we can characterise network
* Home network
* enterprise network

>![[Pasted image 20251002124053.png]] "Routers comes with firewall or net"

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
* physical(then physical layer is responsible for converting my requests into bits and bytes and transferring these bytes correctly over different kinds of communicaiton link such as electromagnetic, light pulses)
# Encapsulation and de-encapsulation
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

In a shared broadcast channel it is possible one reciving node get two transmissions this is called collision domain 
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
		no need for switch to configure to accomade a new hosts.
		how does switch know about connected hosts and how to reach them 
		ans is **self learning**. switch is kind of a plug in-out device no information were installed in switch about the hosts . switch makes  a table take copy about the hosts 
		a well known attacked is called switch poisoning the attacker overwhelms the switch with many frames.
		**Face** : switch table capacity is limited
		attacker sends many frames with bogus source MAC adresses
			no room for legitimate  MAC addressses
			then an attacker runs a sniffer on compromised host
			wireshark packet sniffer tool that can be used to  monitor  all in and outgoing traffic of a host.
**Layer 3**: network layer is responsible for transporting a segment  it recieves from the above layer from sending to receiving host 9example router.) 
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
ex: 222.1.1.0/24x the 24 means here first 3 bytes of the network is same for all possible ip address  meaning 222.1.1.0 to 222.1.1.255 so total 256 possible ip address is possible from that 
network
there are many types network address class A, B, C by in terms of different size of subnet size and host size  subnetting mask donot holds individual host address rather than a subnet


**The address rosolution protoco(ARP) :** very important protocol. stays in between layer 2 and 3. ARP is responsible for mapping ip to mac. when network between in same two nodes they know their ip address not their mac. How they maintain or determie interface's mac knowing its ip only.
Ans: ARP Table at source node. each node whether an end host or router keeps a table of entries and each entries contain a mapping from a destination's node ip address to mac. Each entry also contains an expiration time for the entry to be discarded after certain duration to avoid having stale entries in the table. typically set to 20 min


**We can't send frames using only IP because IP addresses are for identifying nodes across entire networks (Layer 3), while MAC addresses are for identifying specific devices on a local network (Layer 2). IP needs MAC because once a packet reaches its local network, the MAC address is required to physically deliver the frame to the correct destination device on that network. MAC addresses are used for the immediate hop, while IP addresses guide the packet across different networks, with routers using them for the long-distance journey**


thats why we need ARP table. first node A sent to frame to all nodes so we need to send to ARP query in a following format:
A broadcast ARP  query, containing B's ip address 
	**destination mac address = FF-FF-FF-FF-FF-FF**
	all nodes on LAN  recieve ARP query

**ARP Poisoning**: ARP was designed to simple. ARP was also designed with no authentication to verify the ip to map mapping. It can mitigated by using static ARP table that manually maintained by sys admin


**ICMP(Internet Control Message Protocol):** Icmp operates slightly above layer 3 or the network layer. ICMP is used for error reporting on the network. ICMP is used fro a host or router is reachable or not. it stays above network layer and so it carried in ip datagrams.
ICMP message: type, code
client can be replied with type 0 and code 0 means server ok
or 3 0 = destination network unreachable


Traceroute command can help a host  on the network to find the path or route from itself to a target host or router. The command starts by sending a set of UDP segments to the target host which are themselves carried into ip datagrams. 
means set ip datagrams sent by host A. 


**Node to node communication**: when host sending packet to another host and destination the sending host always have the ip of destination(DestIP) but sending host will not always have the mac address. before it sends request for the mac address it firsts check that destination is on the same subnet.
if not then sending host needs to pass the packet to the next hop in line which  will, in turn carry the packet to another remote subnet or the internet. This next hop is in fact called the gateway. if the default gateway is already known to the sending host then the sending host can simply use ARP  to find the MAC address for the default gateway ip and then forward the packet to the gateway. however if the default gateway is not known then the sending host will broadcast a specific ICMP  message called Router Discovery Message to find all the gateways on the same subnet. if the subnet has more than one gateway then the sending host will use the first response it recieves as its gatway. 



# Now we talk about layer 4 and 5

**Layer 4**: its called transport layer right above the network layer provide logical communication between processes running on different hosts. Process is like application like web browser. The transport layer allows a process on one host to talk to another process on anther host this type communication is called inter-process communication.


Two import protocol is
* TCP(Transport Layer Protocol): Reliable, in-order delivery, flow control
* UDP(User Datagram Protocol): unreliable, unordered delivery
A process actually passes a message to the transport layer by a socket.
Socket is like a door in operating system. Last 4 layer in tcp/ip stack controlled by os but application is controlled by app developer. We cant identify a process by ip. but we have so many process. so we have to use a pair of ip address and port number associated with process on host.

in the application layer there are so many processes going on because of port number
by that transportation layer can know which port number it needs to send the message.


both source port and destination ar 16 bits longs. so we can 0 to 2^16-1 total 65536 processes running on a host simultaneously.

The first 0 - 1023 are reseved to well -known application layer protocol. the ohers are used for other user and dynamic application.

**Tcp**: 
* Connection oriented. Connection is established by 3-way handshaking where both sizes exchanges controlled messages. 
* Reliable (in order delivery segment) if segment or packet is lost its enure order. Ex:
	HTTP AND FTP
* Flow controlled : sender will not receiver with lots of data when it cannot recieve such data
in order to act tcp need extra header beside source and dest port number . its called segment seq(counting bytes of data into bytestream not segments)





**Attack in the TCP**:
* SYN Flooding attack : 
* Sequence prediction


**Layer 5 (application layer)**: run on systems, communicate over network , e.g., web server software communicates with browser software. 
 most important protocol is HTTP. 

**DNS(Domaine name system)** : operates at application  obtain an ip address for a specific domain name. DNS is a complex distributive system or database that involves number of server communicated with each other.

Attack on DNS
DDos attacks 
	flood root servers with traffic
	flood tld servers
spoofing attacks 
	intercept DNS queries, returning bogus replies
		DNS cache poisoning
			MITIGATION: DNSSEC(Domaine Name System security exchange) it uses public key cryptography to authenticate

DHCP(Dynamic Host Configuration Protocol): How does host get IP address. sometimes it can be referred network layer protocol rather than application
	* Hard  coded by sysadmin in config file(etc/rc.config by unix)
	* DHCP  dynamically get addressed from a server(follows client server model)
		* plug and play


**Network Firewalls**: It cant do anything in the internal network. it only remove bad guys outside of the network by simply filtering network traffic

its different from IDSes(intrusion detection systems): evaluates a suspected intrusion once it has occured and may signal an alarm

![[Pasted image 20251002135722.png]]


packet filters looks at every packet that enter and leaves in the network  either accept, reject or dropped the packet by some set of rules determined by sys admin. When rejecting a packet it sends a error message in reply. when dropping a packet usually never notify the sender. it only on transport and network layer protocol header for information. 
ip, tcp/ip, icmp. it does not examin application and data section in the packet. 

![[Pasted image 20251002204322.png]]


protocol id value 17 means upper layer protocol used UDP
if value = 6 then tcp if value = 1 then its icmp



SYNbit and ACKbit are two important bit that allows certain connection are alowed or blocked by the packet filter. 

SYNbit is set to 1 and  ACKbit is set to = 0. it uses ACL(Acees Control list) contains a set of rules applied starting from the top and working its way down the list . packet filter follow fail safe design principle means by default they block all the traffic untils a sys admin allows a certain traffic. thats why the last rule is deny all traffic.
![[Pasted image 20251004101445.png]]

stateless packet filter does not track status of every TCP connection and doesnot save the state. pros: lower overhead, time and memory 
	cons: ip spoofing

state packet filter save status aware of ip spoofing stored all on-going connections in a connection state table

 **others filewall**




**Port Scanning**: Port scanning in a host scanned all the ports that are opening and listen to the request. Port scanning tools can also report if the host machine is behind a firewall.
Most popular tool for post scanning is **nmap** is free and open source. most common scans are the SYN, CONNECT, FIN, ACK scans

**SYN scan**: 
* the default
* uses raw sockets to construct the syn packet  (requires permisision)
* does not complete the handshake process
now look at how syn scan works

first nmap send syn packet to the target host at that port. if it closed and not accepting any request then the port sends a reply with RST.

for open port, syn packet is sent to target host port then port allocate resource for that request replies with syn ack. then nmap sends RST to release resources because nmap now konw that this port is open
![[Pasted image 20251004123446.png]]

syn scan is very important because all tcp connection starts with syn scan

**connection() scan**: uses the connect() api call offered by the os (no permissoin needed)
Does complete the handshake process
![[Pasted image 20251004123820.png]]

**Fin scan**: 
* uses raw sockets requires permissions
* target machine responses is differed from target operating systems
	* linux TCP RFC recommendation
		* closed port RST
		* open port do not respond
	* windows os has its own tcp implementation
		* always replies with RST
* can only determine if closed of open/filter
	* difficult to determine open port since firewalls dropping FINS also do not respond


![[Pasted image 20251004124650.png]]

useless for windows

![[Pasted image 20251004124723.png]]

just like **syn scan** FIN scan just not appear in the host application logs

**ACK SCAN**: use raw socket to construct the ack packet. requires permission

**Target machine response**: 
* no firewall:  always reply with RST
* firewall: never reply or reply with icmp unreachable error

![[Pasted image 20251004125432.png]]