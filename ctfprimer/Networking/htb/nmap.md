Time to Live (TTL) values dictate the maximum lifespan of a data packet or cached record on a network before it's discarded, with values ranging from 1 to 255 for network packets or specific time durations for cached DNS or web content.

- Linux/macos - 64
- windows - 128
- cisco router - 255

when a packet get filtered that means it get caught by firewall and only two possible things can happen
- packets can be dropped (its ignored and no response is returned from the host)
- packets can be rejected(send an ICMP explicit responses )



SYN scan is the “polite knock on the door”.

- Nmap sends a **SYN** to a port.
    
- **If the port is open**:
    
    - Target replies with **SYN+ACK (SA)** = “Sure, let’s talk.”
        
    - Nmap then sends RST to avoid completing the full handshake (stealth / half-open scan).
        
- **If the port is closed**:
    
    - Target replies with **RST (R)** = “No service here.”
        
- **If filtered by firewall**:
    
    - Nmap gets **no reply** or an ICMP error.


So the **RST in ACK scan is normal and expected**, and actually useful:

- **RST received** → host saw your packet → port is **unfiltered** (reachable).
    
- **Nothing received** → something (firewall) is blocking or dropping that traffic → port is **filtered**.


Analogy:

- You suddenly say “yeah I agree” (ACK) to a person you never started a conversation with.
    
- If they hear you, they respond “What? We weren’t talking, stop.” (RST).
    
- If they never hear you because a guard stopped you at the door (firewall), they say nothing.