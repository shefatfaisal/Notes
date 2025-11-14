
**4 Core features of an os**
- Process management
- memory management
- file system management
- peripheral management


Non-critical os features
- Graphical user interfaces
- network management


4 types of terminology are used 

- Security subject/principal - Anything whose identity can be authenticated
- Security object - Anyhting that can be accessed
- Permissions - the rights of a security principal on a security object
- Reference Monitor - the entity that enforces access control
![[Pasted image 20251110232018.png]]

**Authentication models**

- Local Authentication
- Single sign-on

**Isolation**
In most modern operation systems isolation is achived theoratically by Bell-LaPadula model

in this model 
- Every security subject has a clearence level
- Every security object has a classifcation level

Two conditions
- simple security conditon: A subject can read an object if and only if the clearence of the subject is greater or equal to the classification of the object
- Star property(Paraphrased): you cant create a security object that has classification level lower than your clearence level
![[Pasted image 20251111114838.png]]


that means subject can write security object that is same and upwards level but down and can read object same and down level


**Classification level**
Government
- Sensitive
- classified
- secret
- top secret
	- Sensitive Compartmented Information(SCI)

Civilian
- proprietary
- private
- confidential
- sensitive

SCI we name compartments as some level

but in Bella la padula model is actually implemented in operating system which is called rings of protection


**Properties of Modern Malware**
- Memory Resident or Fileless
	- only ever exists in RAM
	- Hard to detect - Antivirus commonly monitor files
	- Highly volatile - When the computer reboots, malware goes away
	- very used in mobile
- Disk-resident or File-based
	- program or code is written to the hard disk at some point
**Modern malware functions:**
- Worms - marware is self-replicating
- Trojan- Malware provides remote access to adversary
- Ransomware - Malware disables system functionality until a ransom is paid
	- most commonly, ransomware encrypts files in hard drive
- Adware - Malware displays unwanted advertisements to the user
- spyware -


Trojans: Bind shells vs reverse shells

Bind shell - attacker intiated the communication
reverse shell - target initiated the communication


reverse shell are defeated by outbound firewall but its so much harder to setup rules for outbound firewall because every user use port 80 for http so organization doesnot want to block that ip


**Advanced isolation**

- Virtualization
- containerization
![[Pasted image 20251114023527.png]]

**Logs:**
![[Pasted image 20251114023747.png]]


**Anti virus/anti Malware software**

- Primary  detection and prevention mechanisms
- More commonly used in workstations (laptop, desktop)
-  Two primary detection mechanisms
	- Signature-based detection
	- anomaly based detection