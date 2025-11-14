elements of a wireless network 
**Wireless hosts**
- it connects with a base station
- they operate in an infrastructure mode 
- mobile changes base station we called this process handoff
- ad hoc mode is no base stations is connected with the host 
- ![[Pasted image 20251009011733.png]]

**Wifi** :
- lower frequency travels longer distances
- wireless host communicates with base station = access point(ap)
- so they form Basic Server Set(BSS) or cell infrastructure
	- wireless hosts
	- access point(ap): base station


lets see how an ap is set up by an network admin it assigns a service set identifier or ssid to the access point to operate in one of the 11 freuency channels available now wireless host wants to associate with the ap, it has to first scan the 11 channels and see if any of the ap in the area are broadcasting beacon frame(so access point )  to advertise its presence and network details. Each beacon frame contains the ssid and the mac address of the ap. then the host select the ap. Then the host dynamic ip from the DHCP to get ip address in AP's subnet.

![[Pasted image 20251009014702.png]]


**Access point is link layer device or layer 2 device just like switch it doesnot talk with ip addresses**


Wifi frame have 3 mac addresses but ethernet frame have 2 so we need to convert wifi frame to ethernet frame because ap is connected with Router via a wire

ap cannot do arp to find mac address given destination ip address thats why 
![[Pasted image 20251009020201.png]]
thats why in ethernet frame it contains r1 mac addr and h1 mac addr but not its mac add.


PAN(Personal area network):
- less than 10m
- single hub network 
- master device (laptop) and client device(mouse, keyboard)
- have up to 8 active device including master
- and 255 parked inactive devices in the network and they cannnot talk with master unless master active them
- 3mbps

**Challenges**:
- Traffic sniffing/eavesdropping tools like wireshark we call these passive attack
- certain iot devices are resource constraint battery power limited transmitting packets accross the wireless medium already causes such devices  to drain a lot of energy to design security protocols algorithms should be lightweight so that they cannot do any overhead to the power
**goals**
- CIA triad
- AAA(authentication, authorization, accountability) 
**Protocols**:
- WEP(wIred equivalent Privacy)
- WAP(wifi protected access)
- wpa2 - offered two types of authentication
- ![[Pasted image 20251009024458.png]]



![[Pasted image 20251009022716.png]]