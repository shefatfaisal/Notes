![[Pasted image 20260407134436.png]]
![[Pasted image 20260407134451.png]]

dockercompose.sh
```bash
#!/bin/bash
sudo dnf install libxcrypt-compat -y
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
ls /usr/local/bin/
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose version
```
sample docker-compose.yml

```bash
version: '3.8'  
services:  
 internetbanking:  
   image: internetbanking:v1  
   ports:  
     - "81:80"  
 mobilebanking:  
   image: mobilebanking:v1  
   ports:  
     - "82:80"  
 loans:  
   image: loans:v1  
   ports:  
     - "83:80"  
 insurance:  
   image: insurance:v1  
   ports:  
     - "84:80"
```

![[Pasted image 20260407143513.png]]


here we are creating load balancer using nginx and try to connect this with docker


