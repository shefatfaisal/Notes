
1950 revolution to make container for shipping by maclaren

Dock -> port

Docker is a person who loads and unloads from the ship in the dock

Docker is not a tool its a platform

what is kernel: Brain of os. take control over everything like hardware, ram, cpu
- process management
- devcie management
- system call 
- memory management
- file system management

CPU has two mode:
- kernel mode(execute code in kernel space)
- user mode(execute application in user space) they dont have direct access in kernel space 
	- they can though system call to have access to excess hardware and some other resource in kernel
	- also one application cant take access resource of other resource


**virtual machine vs container**
vm has its own os but container doesnot

**Docker engine**: most important path. can create container using namespace and cgroups

linux has namespace and cgroup feature so docker engine can take access linux kernel


**Docker uses namespace and cgroup to create processes seperate by default**.



**NAMESPACE**:  it seperates two process from each other so one process cant access another process resources. 

**CGROUP**: It can limits how much a process access cpu resources.

   
but in windows and mac they dont have any namespace and cgroup so when we download docker engine it automatically installs a docker desktop that creates vm of mini linux

==Docker Engine can create multiple containers in my kernel using **namespace and cgroup**==




task of docker engine:
- create containers
- delete container
- stop container
- clean container
- build image

3 parts in docker engine:
- dockerd -> docker daemon background process, silently, invisible
- cointainer runtime
	- containerd (high level container runtime)
		- manages container lifecycle
		- manages container's storage
		- manage image
		- also provide api to talk with docker daemon
	- runc (low level container runtime)
		- create, start, stop container by creating namespace and cgroup
![[Pasted image 20260118210049.png]]
