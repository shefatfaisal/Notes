we can remotely connect to the windows machine

command for that
```
xfreerdp /v:<ipaddress> /u:<username> /p:<password>
```




There are 5 types of windows file systems

- FAT12
- FAT 16
- FAT 32 
- NTFS THIS IS MAIN
- exFAT


**SMB:**
Sometime a file was given access to everyone and share  using SMB protocol but othter cant access them because of Windows defender firewall and the accout that gonna access the file is stored in local SAM in a workgroup or ACTIVE DIRECTORY  in a domain.

There are Two different ways of organizing windows computers on a network
- workgroup
- Domain
**Workgroup**: Peer to peer, every box on its own. So basically Each individual machine has its own SAM(Security Accounts Manager) database. In workgroup every machine checks its own SAM for authentication.


**Domain:** A domain has one or more domain controller. The DC runs **Acitve Directory(AD)** which holds the central user and group database. When someone log in with a domain account on domain-joined machine
- The machine sends the logon request to the DC which checks Active Directory.
But in domain it can have local account too. For local accout it authenticated by SAM 