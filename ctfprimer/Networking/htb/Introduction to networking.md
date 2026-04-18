- wan (internet) - Many large companies or government agencies will have internal wan its called intranet
- lan/wlan ex: hotel, college network
- vpn making the user feel as if they were plugged into a different network
	- site-to-site vpn
	- remote access vpn
	- ssl vpn
**Split-tunnel vpn**: if connects to a vpn host machine  get a ip address and creates a TUN adapter so that it behaves like it physically plugged into that network and then its create a routing table so that if some traffic goes to the TUN it redirects that traffic to that direction and other like watching yt videos redirects to home wifi

**Full-tunnel vpn:** but for companies this is bad idea for splitting the traffic. because employe's effected then malware taliking to a c2 server on the internet might go directly out employe's home connection bypassing company monitoring.

That means:
- **Network-based detection systems** (IDS/IPS, firewalls, etc. inside the company) never see that malicious traffic.
    
- The company loses visibility and control over part of what the laptop is doing.


**SSL VPN**: 
Think of this as “VPN without installing a VPN app.”

I will break it into pieces.

---

### 1. “VPN in the web browser”

Normally, a VPN means:

- You install a VPN program
    
- It creates a virtual network card on your computer
    
- All or some of your traffic goes through that secure tunnel
    

Here, the idea is similar but done through a web page.

You open a website  
The site gives you access to a machine that is already inside the special network  
Anything you run in that browser window talks to the lab network as if it is on that network

So you are not changing your own computer network settings  
Instead you are using a remote computer that is already connected