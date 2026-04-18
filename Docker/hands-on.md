
how to pull a image

docker pull ubuntu:24.04

how to run 

docker run -it ubuntu:24.04 bash -> its gonna create a container for ubuntu version 24.04

we can create multiple container for this ubuntu by default docker gives it a random name but we can give ours by following this command


docker run --name my_name -it ubuntu:24.04 bash

so again if we want to access this container 

we can do by

docker exec -it my_name(or we can give container id too)



here -it means
i means **standard input**
t means  pseudo tty(terminal).


**we can create our customize docker images using docker build command and can give it a name** 


**How to copy a file from host machine to docker container**


sudo docker cp sourch dest



