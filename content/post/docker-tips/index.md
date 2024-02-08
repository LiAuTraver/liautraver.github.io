---
title: "Docker Tips"
description: 
date: 2024-02-08T20:08:01+08:00
hidden: false
comments: true
draft: false
categories:
    - Experience
tags:
    - Docker
---

## What is Docker?

When developing applications, it is common to have different environments for development, testing, and production. This can lead to "it works on my machine" problems. Docker solves this problem by creating a consistent environment for applications to run in.

As its name, `docker`, a platform for developers and sysadmins to develop, ship, and run applications. 
Docker itself will automatically package an application and its dependencies in a container
that can run in any environment.

#### Container and Virtual Machine

We have all familiar with virtual machines, such as WSL on Windows, which are a complete operating system and application running on top of a hypervisor. 

But there are some problems. Virtual machines are highly resource-intensive, for each VM needs the entire operating system to be loaded and running. That means without a full-blown OS, the VM can be quite large and slow to start. 

Containers are similar to virtual machines, but they are more lightweight and share the host system's kernel. They do not need a whole operating system, which means they consume fewer resources such as CPU, memory, and storage. All containers share the same OS kernel. macOS does not support containers natively, Docker for Mac uses a Linux VM instead. On Windows, ensure the Optional feature `Hyper-V` and `Containers` are enabled.

## Build and Run a Docker Container — Hello Docker

To build a Docker container, we need to create a `Dockerfile` which contains the instructions to build the container. For instance, we make a simple Docker image that runs a `node.js` application.

```Shell
# Create a new directory
mkdir hello-docker && cd hello-docker
vim app.js
```

```Javascript
console.log('Hello, Docker!');
```

Docker builds and runs an image from a `Dockerfile`, which is a text file that contains all the commands a user could call on the command line to assemble an image.

```Shell
vim Dockerfile
```

```Dockerfile
# Use an official Node.js runtime as the base image
FROM node:alpine
COPY . /app
WORKDIR /app
CMD node app.js
```

All done! Now we can build the Docker image and run it.

```Shell
docker build -t hello-docker:test . # build the image named `hello-docker` with tag `test`
docker image list # list all images
docker run hello-docker:test # run the image
```
output:

```Shell
Hello, Docker!
```

Note: Docker images aren't stored in the current directory, but in the Docker daemon's storage area.

```Shell
docker ps # list all running containers
docker ps -a # list all containers
```

## Docker Hub

Docker Hub is a cloud-based registry service for Docker, just like GitHub for Git. It allows you to push and pull images from the cloud and share them with others.

```Shell
docker login # login to Docker Hub
docker push hello-docker:test # push the image to Docker Hub
docker pull hello-docker:test # pull the image from Docker Hub
```

## Run a Ubuntu Docker Container

```Shell
docker run -it ubuntu:latest /bin/bash
```

`-it` is a combination of `-i` and `-t`. `-i` stands for interactive, `-t` stands for terminal. It allows us to interact with the container. Otherwise, the container will exit immediately after running the command.


