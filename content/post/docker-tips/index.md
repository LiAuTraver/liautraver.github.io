---
title: "Decoding Docker: Enhancing App Development"
description: "A brief introduction to Docker."
date: 2024-02-08T20:08:01+08:00
image: "docker.png"
comments: true
draft: false
categories:
    - Experiences
tags:
    - Docker
---

## What is Docker?

When developing applications, it is common to have different environments for development, testing, and production. 
This can lead to "it works only on my machine" problems.
That's why [Docker](https://www.docker.com/) comes in.

As its name, Docker adeptly encapsulates applications and their dependencies,
ensuring seamless operation in any environment.
This systematic approach not only addresses compatibility concerns
but also streamlines the development and deployment processes with precision.

## Unpacking Containers vs. Virtual Machines

We're all acquainted with virtual machines like WSL on Windows – complete operating systems and applications running atop a hypervisor. 
However, they come with their fair share of challenges.

Virtual machines tend to be resource-intensive, 
for each VM demands the entire operating system to be loaded and running,
making them sizable and slow to start without a full-blown OS.

On the other hand, containers are a more efficient alternative when it comes to application development and deployment.
Containers are lightweight and cozy up to the host system's kernel. 
Unlike VMs, they don't require a full operating system, 
resulting in more frugal consumption of resources such as CPU, memory, and storage.
All containers share the same OS kernel.
While macOS doesn't naturally support containers, Docker for Mac ingeniously employs a Linux VM. 
On Windows, make sure to activate the Optional features Hyper-V and Containers to dive into the container magic.

## Build and Run a Docker Container — Hello Docker

To build a Docker container, simply create a `Dockerfile` which contains the instructions to build the container. 
For instance, I'll show how to make a simple Docker image that runs a `node.js` application.

```Shell
# Create a new directory
mkdir hello-docker && cd hello-docker
```

Create a new file named `app.js` and add the following content:

```Javascript
console.log('Hello, Docker!');
```

## Dockerfiles: Crafting Your App's Blueprint
Docker builds and runs an image from a `Dockerfile`, which is a text file that contains all the commands a user could call on the command line to assemble an image.
Think of it as the blueprint for your app's ship.
This cool text file is where you sketch out all the steps, just like a handy guide, for Docker to build and run your image, 
which also makes it possible to share your app with others without worrying about the environment.

Create a `Dockerfile` using Vim(or whatever) and add the following content:

```Dockerfile
FROM node:alpine
COPY . /app
WORKDIR /app
CMD node app.js
```

Here's a breakdown of the `Dockerfile`:
1. `FROM node:alpine` — Use the official Node.js runtime as the base image. The `alpine` tag is a lightweight version of the Node.js image which only needs 150MiB storage more or less. Some Docker images are extremely large, but the `alpine` tag is a minimal image that's perfect for small applications.
2. `COPY . /app` — Copy the current directory to the `/app` directory in the image. The `.` refers to the current directory, and `/app` is the directory in the image.
3. `WORKDIR /app` — Set the working directory to `/app` in the image. This is the directory where the command will be run.
4. `CMD node app.js` — Run the `node app.js` command when the image is run.

As for large applications, Dockerfile might be more complex, but just like CMake, we don't need to know it comprehensively. 
Check the [official documentation](https://docs.docker.com/engine/reference/builder/) and learn when necessary.

All done! Now we can build the Docker image and run it.

```Shell
docker build -t hello-docker:test . # build the image named `hello-docker` with tag `test`
docker image list # list all images
docker run hello-docker:test # run the image
```
The output should be:

```Shell
Hello, Docker!
```

Congratulations! You've just built and run your first Docker container.🚀✨

Note: Docker images aren't stored in the current directory, but in the Docker daemon's storage area.

```Shell
docker ps # list all running containers
docker ps -a # list all containers
```

## Docker Hub

[Docker Hub](https://hub.docker.com/) is an official cloud-based registry service for Docker, just like GitHub for Git. It allows you to push and pull images from the cloud and share them with others.

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


