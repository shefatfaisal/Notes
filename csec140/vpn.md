Virtual private network built over existing physical network(the internet)
* all trafic is private(encrypted)
* goal: protect traffic carried over the public internet
Why vpn 
	By building own physical network is very costly and not scalable what if company open a new branch
**Tunneling**: Vpn achieve this goal by create tunnel between headquarter and employee
This tunnel is achieved by vpn connection between two ends of the tunnel
	Client-side 
		vpn software
	Server-side
		vpn server software
		vpn applicance
		firewall/vpn
		router with vpn
**vpn traffic can also be flowing between two routers equiped with a tunneling protocol its called site-to-site vpn communication**
**A site-to-site VPN** creates a secure, encrypted tunnel over the internet to connect two or more separate networks, such as a company's headquarters and its branch offices, allowing them to share resources as if they were on the same local network![[Pasted image 20251009000417.png]]


**Tunneling Protocols**
PPTP(Point to point Tunneling Protocol):
* old and insecure
* uses DES to encrypt the authentication key
* provides no data integrity check
* still widely used
L2TP(Layer 2 Tunneling Protocol): it called layer 2 cause it enncrypts entire link layer frame instead of IP datagram over the network
- L2TP alone does not encryption and authetication
- it uses IPsec to provide confidentiality, authentication and integrity 
IPsec: open standard and part of the ipv4 suite
- it requires third party vpn client software for supporting remote employees
- ipsec has port number set to 50
- confidentility 3des/AES
- data integrity
	- sha1/sha2
- origin authtication
	- HMAC
- Replay attack prevention
	- Sequence number to prevent this type of attack
**Two types of tunneling methods**

SSL (Secure socket layer): in this vpn client connects to the VPN server via port 443 
Two types:
- clientless or ssl portal vpn(no need for client software for non- technical user)
- client based of ssl tunnel vpn(need software)



