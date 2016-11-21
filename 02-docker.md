# Introduction to Docker Containers

## Virtual Machines VS Containers

<div style="text-align:center">
  <img src ="/assets/img/vmVSContainer.png"/>
</div>

## Docker Images

Docker images are the basis for container, like for example the ubuntu image. Each image references a list of read-only layers stacked together.

## Docker Volumes

If we want to have persistent data we need to use Docker Volumes. A volume is a special directory of the container initializes when the container starts.

Data Volumes can be shared by containers and changes to the data will go directly to the host system and will not be saved in the image layers.

To create a data volume you need to create a mapping between a container directory and a directory on the host system, like for example:

    docker run --name welcome -v /data/webapp:/webapp -t -i ubuntu:1604 /bin/bash

This command launches a Ubuntu 16 container and mounts the directory **/data/webapp** from the Host into the container as **/webapp**

### Docker Volumes Plugins

You can extend the capabilities of the Docker Engine by loading third-party plugins. They come in specific types. For example, a volume plugin might enable Docker volumes to persist across multiple Docker hosts and a network plugin might provide network plumbing.

Currently Docker supports authorization, volume and network driver plugins. In the future it will support additional plugin types.


### Flocker Plugin

Flocker plugs into Docker to ensure that Shared Storage can mapped to the Server where the container runs on.

Is a Server crashes, Flocker can remap the storage to another server, and the container can now be started on the other server using the same data.

## Docker Networking Overview

Docker Containers connect to a bridged network by default use port mapping but is difficult to mantain. A better way is to create **user defined networks**. Containers within the same user defined network can communicate with each other but cannot communicate with containers outside the network.

### Create User Defined Bridge Network

Create a new Bridge Network:

    docker network create --driver bridge my_network

Launch container in new network:

    docker run --net=my_network ....

If we have external container (in a different Docker Host) we need to specify the network and the port mapping:

    docker run --net=my_network -p 80:80 ....

## Docker HUB

Is a service that provides public / private registries for Docker images. Click [HERE](https://hub.docker.com/)

    https://hub.docker.com/

## Docker Compose

Compose is a tool part of Docker to define your multi-container docker environment as a code that can manage the lifecycle of a docker container. Typically you write a Dockerfile for every container you want to launch and then you launch and administrate the containers with the **docker-compose** command.
