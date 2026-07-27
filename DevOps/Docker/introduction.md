# Docker:
problems : 1. extra efort for set up a project 2.version didferent 3. took issue os specific. 4. env raplication is the mean issue 
solution : container 

# instruction of docker CLI and Deshtop

hub.docker

youtube link [https://www.youtube.com/watch?v=31k6AtW-b3Y] [https://www.youtube.com/watch?v=xPT8mXa-sJg] [https://www.youtube.com/watch?v=AiiFbsAlLaI]

# images v/s containers : [https://docs.docker.com/get-started/docker-overview/]

## Images:

" An image is a read-only template with instructions for creating a Docker container. Often, an image is based on another image, with some additional customization. For example, you may build an image that is based on the Ubuntu image but includes the Apache web server and your application, as well as the configuration details needed to make your application run."

You might create your own images or you might only use those created by others and published in a registry. To build your own image, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it. 
#### Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

## containers :
Containers
A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI. You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.

By default, a container is relatively well isolated from other containers and its host machine. You can control how isolated a container's network, storage, or other underlying subsystems are from other containers or from the host machine.

A container is defined by its image as well as any configuration options you provide to it when you create or start it. When a container is removed, any changes to its state that aren't stored in persistent storage disappear.



# running ubunto img in container

# multiple container

# enviromment variables

# docarization on notejs file

a. Dockerfile
b. Caching Layers
c. Publishing to Hub

# docker conpose:

a. servise
b. port mapping 
c. ENv Variable


part- 2 Docker networking

1. Docker networking

a. bridge 
b. Host
d. none

constome network creation 

2. volume mounting and docker volume
note: when stoping the container the memory is deleted so docker volume helpes prevent this.

3. effecent caching in layers
 docker ingnore

4. Docker multi stage build 


systemctl --user start docker-desktop