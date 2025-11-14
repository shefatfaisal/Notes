
When pinging a remote server, the sender's MAC address in the ARP section of a Wireshark capture will not be the same as if you were pinging a local device.

Here's why:

- 
    **Local Ping:**
    When you ping a device on the same local network segment, your computer needs to resolve the target's IP address to its corresponding MAC address using ARP. In this case, the ARP request and response will contain the MAC address of your computer's network interface card (NIC) as the sender's MAC address, and the MAC address of the target local device as the target MAC address in the ARP response.
    
- 
    
    **Remote Ping:**
    When you ping a remote server (a device outside your local network segment), your computer does not directly communicate with the remote server's MAC address. Instead, your computer sends the ICMP echo request to its default gateway (router). The router is responsible for forwarding the packet to the remote network. Therefore, the ARP request your computer sends will be to resolve the router's IP address to its MAC address. The ARP section in Wireshark will show your computer's MAC address as the sender and the router's MAC address as the target. The remote server's MAC address will not be directly visible in the ARP traffic originating from your computer.

CSE-111 includes propositional/predicate logic, proof techniques (direct, contradiction, induction), sets/relations/functions, counting and recurrences, growth of functions, and graphs/trees—all core outcomes for RIT MATH-190