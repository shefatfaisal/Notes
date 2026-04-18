**Virtual Private Cloud** : VPC is private cloud inside AWS.

2 vpc's cannot communicate with each other by default, if required yes 


![[Pasted image 20260110111946.png]]

VPC is regional and every region has default **vpc**
We can create maximum 5 vpc


**DirectConnect**
In general company use vpn to connect to the AWS
In AWS there is a service where we can have direct connect to AWS is called **Direct Connect**
![[Pasted image 20260115004956.png]]


![[Pasted image 20260308140552.png]]![[Pasted image 20260309004314.png]]


 ## **Connectivity Options**

### VPC Peering

- Direct network connection between **two VPCs** (same or different accounts/regions)
- Traffic stays on the AWS backbone — no internet
- **Not transitive**: A↔B and B↔C does NOT mean A↔C

### VPC Endpoints

Allows private connectivity to AWS services **without using the internet**:

- **Interface Endpoint** — uses a private IP in your subnet (powered by AWS PrivateLink)
- **Gateway Endpoint** — used for S3 and DynamoDB only; added to route tables

### Transit Gateway (TGW)

- A **hub-and-spoke** model for connecting many VPCs and on-premises networks
- Solves the complexity of full-mesh VPC peering
- Supports **route tables**, enabling traffic segmentation

### VPN & Direct Connect

|Option|Description|
|---|---|
|**Site-to-Site VPN**|Encrypted tunnel over the internet to on-premises|
|**Client VPN**|Remote users connect securely to VPC|
|**AWS Direct Connect**|Dedicated physical connection to AWS (low latency, consistent bandwidth)|
### Elastic Network Interface (ENI)

- A virtual network card attached to an EC2 instance
- Has a primary private IP, optional secondary IPs, and a MAC address
- Can be detached and moved between instances (useful for failover)


AWS provides a **DNS resolver** at the base of your VPC CIDR + 2 (e.g., `10.0.0.2`)

|Your Home|AWS VPC|
|---|---|
|Your Router|The entire VPC|
|DHCP from router|AWS assigns private IPs to EC2 instances|
|Your private IP (192.168.x.x)|EC2's private IP (10.0.x.x)|
|Your default gateway (router's LAN IP)|The **implicit VPC router** (always at subnet's first IP + 1)|
|Router's public IP (from ISP)|**Elastic IP / Public IP** on NAT Gateway or EC2|
|Router doing NAT|**NAT Gateway**|
|Your modem connecting to ISP|**Internet Gateway (IGW)**|


# cloud formation

this is a service to automate creating vpc, subnet etc by writing code **(Infrastructure as Code (IaC))** so IaC tools are: Terraform, Ansible, aws cloud formation

Terraform can be used any cloud provider here we will aws cloud formation to automate vpc and other infrastructure

![[Pasted image 20260309231909.png]]




![[Pasted image 20260309231741.png]]

here we want to ssh from Mumbai vpc private box to ireland vpc private box through vpc peering