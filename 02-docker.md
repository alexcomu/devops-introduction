# Microservices

Monolithic application is **BAD**:

* Easy to develop, test and deploy
* Tend to become large and complex
* Difficult to work on as a team
* Higher risk of failure when deploying

So, Microservices are **better**:

* Monolithic application divided into smaller "microservices"
* Smaller service can use its own technology stacked
* Easier for developers to understand a single services
* Quicker to build and faster to deploy

# Docker

Docker is an open platform for developers and sysadmins to build, ship and run distributed applications:

* Docker Engine: a portable, lightweight runtime and packaging tool
* Docker Hub: a cloud service for sharing applications and automating workflows
* Docker enables apps to be quickly assembled from components
* Docker eliminates the friction between Dev, QA and production environments
* It should be able to ship faster
* Should be able to run the same app, unchanged, on laptops, data center VMs and cloud.
* Docker uses LXC (Linux Containers) for operating system-level virtualization.

## Dockerfile

Is a text document that contains all the istructions and commands used to build a Docker image. We can use **docker build** to create an automated build that runs each istruction from the Dockerfile.

### Dockerfile example

For example, if we want to create a simple image that runs a basic Nodejs application with should write a Dockerfile like the following

    FROM node:0.12
    WORKDIR /app
    ADD ./app
    RUN npm install
    EXPOSE 3000
    CMD node index.js

Now that the Dockerfile is ready we need to run the build command:

    docker build -t alexcomu/MYNODEAPP .

We can now run the Node app:

    docker run -p 8080:3000 -t alexcomu/MYNODEAPP

Opening a Browser we can go to the link: **http://localhost:8080** and we'll see the node application.

# Introduction to Containers

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

### Docker compose EXAMPLE

First of all we need to install docker-compose:

    curl -L "https://github.com/docker/compose/releases/download/1.9.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose
    docker-compose --version

Here an example of a **docker-compose.yml** file:

    web:
        build: .
        command: node index-db.js
        ports:
            - "3000:3000"
        links:
            - db
        environment:
            MYSQL_DATABASE: myapp
            MYSQL_USER: myapp
            MYSQL_PASSWORD: mysecurepass
            MYSQL_HOST: db
    db:
        image: orchardup/mysql
        ports:
            - "3306:3306"
        environment:
            MYSQL_DATABASE: myapp
            MYSQL_USER: myapp
            MYSQL_PASSWORD: mysecurepass

We have defined two different containers, one for a simple Nodejs Web app and one for a mysql database.
To run the database and then the node app use the following steps:

    docker-compose up -d db
    docker-compose up -d web

To turn off all use the command **docker-compose down**.

## Docker Machine

Docker Machine is a tool that lets you install Docker Engine on virtual hosts, and manage the hosts with docker-machine commands. You can use Machine to create Docker hosts on your local Mac or Windows box, on your company network, in your data center, or on cloud providers like AWS or Digital Ocean.

Using **docker-machine** commands, you can start, inspect, stop, and restart a managed host, upgrade the Docker client and daemon, and configure a Docker client to talk to your host.

## Docker Swarm

Docker Swarm is native clustering for Docker. It turns a pool of Docker hosts into a single, virtual Docker host. Because Docker Swarm serves the standard Docker API, any tool that already communicates with a Docker daemon can use Swarm to transparently scale to multiple hosts.

Like other Docker projects, Docker Swarm follows the “swap, plug, and play” principle. As initial development settles, an API will develop to enable pluggable backends. This means you can swap out the scheduling backend Docker Swarm uses out-of-the-box with a backend you prefer. Swarm’s swappable design provides a smooth out-of-box experience for most use cases, and allows large-scale production deployments to swap for more powerful backends, like Mesos.
