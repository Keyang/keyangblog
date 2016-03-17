title: Docker Quick Notes
author: Keyang Xiang
date: 2016/2/10
tag:
  - docker
categories:
  - tech
---

# What is Docker and why?
[See here](https://www.docker.com/what-docker)

In short:

* Docker is a container tech based on LXC
* Much less resource than VM by sharing cores
* But provide full run-time isolation
* It makes system deployment much faster
* and easier system operation

Still don't know why? Check out [use cases here](https://www.docker.com/use-cases)
<!-- more -->
# Docker Engine

## General

Docker engine is a command line which interacts with docker daemon to perform RESTful web requests like pulling images, spawn containers etc.

Once docker being installed successfully, it is able to use `docker` in command line to run docker engine.

## Cannot connect to the Docker daemon. Is the docker daemon running on this host?

This error is given if `docker` cannot connect to $DOCKER_HOST.

There are 3 env vars needed to run `docker` command correctly:

* DOCKER_HOST: The docker daemon host. It could be something like "tcp://xxx.xxx.xxx.xxx:2376" depends on daemon configuration.
* DOCKER_TLS_VERIFY: Whether to verify the connection is TLS or not. It could be "1" -- TLS only or "0" -- TLS disabled. For production "1" is a must be.
* DOCKER_CERT_PATH: If TLS is enabled, where to find related certs. It points to a folder containing current client private key, certificate and a trusted CA certificate. (ca / cert / key).pem. For more information about TLS, please see [here](https://docs.docker.com/engine/security/https/).

To switch between Docker daemon instances, change the 3 env vars above to correct ones. With help of [Docker Machine](#docker-machine), we do not need to do this manually.

## Tips

### Run a docker image

```bash
docker run -itd <image name>
#e.g. docker run -itd nginx
```

This command will pull the image from docker hub if image not existed and then run the default command of the image.

* -i: Keep STDIN open
* -t: Allocate a pseudo-TTY. This is essential for logs which directly write to stdout/stderr
* -d: Detached / run the image in background

### Run a one-time command in a docker image

```bash
docker run -it --rm <image name> <path to shell or bash>
#e.g. docker run -it --rm nginx bash
```

This will promt a bash shell allowing typing commands. Typically using this to debug an image see if everything in Dockerfile is correct.

* --rm: Remove the container after container exited. Without it, the container will remain in container list `docker ps -a `.

### Run a command in a running docker container

```bash
docker exec -it <container name or hash> <path to command>
#e.g. docker exec -it my_nginx_1 bash
```

This will make the **running** containner to run the command provided. This is very useful to debug a running container.

# Docker Compose

Docker compose is a single configuration file containing multiple docker containers (services). It allows batching running / stopping.

The power of docker compose is it gives very clear architecture of system:

* The dependencies of each component (services)
* How services are allocated to different servers (nodes)
* How network is configured
* What ports have been exposed to public
* What environemnt has been configured to each service.

## Wordpress Example (docker-compose.yaml):

```yaml
version: '2'
services:
  nginx:
      image: nginx
      ports:
        - 80:80
        - 443:443
      links:
        -blog
      volumes:
        - /opt/www:/opt/www:ro
        - /opt/nginx:/etc/nginx/conf.d:ro
  blog:
    image: wordpress
    links:
      - blog_db
    environment:
      - WORDPRESS_DB_HOST=blog_db
      - WORDPRESS_DB_PASSWORD=xxxxxx
    volumes:
      - /opt/www/blog:/var/www/html

  blog_db:
    image: mariadb
    environment:
      - MYSQL_ROOT_PASSWORD=xxxxxx
    volumes:
      - /opt/data/blog:/var/lib/mysql:rw
```

The example above show the cluster has 3 parts which compose a wordpress blog system.

For more about docker compose. See [here](https://docs.docker.com/compose/overview/).

## Network && Links

Docker compose will automatically create a overlay network which has all services registered under same network. That means all service can access each other through its name. In the example, it is able to `ping blog` from *nginx* container.

# Docker Machine

Docker machine is a VM level tool. It helps provision a bare VM (or non-bare VM) to be docker ready.

It is highly recommended to provision Docker daemon using this way on your own VM.

It has a bunch of built-in drivers like digital ocean, aws etc. However, as long as you got SSH access and root account, it should not be a big problem.

## Example: provision on Digital Ocean

```bash
docker-machine create -d "digitalocean" --digitalocean-access-token "<digitalocean api token>" -digitalocean-region "lon1" my-dm
```

* -d: driver to use. See [here](https://docs.docker.com/machine/drivers/) for list of drivers.

Once it is finished. The new machine can be found with `docker-machine ps`.

## Point to a machine

```bash
eval $(docker-machine env <my-dm>)
```

This command will set essential env vars for [docker engine](#docker-engine).

## Backup your machines !!!

The docker-machine will use <HOME>/.docker/machine to store all your certs / configurations.
Backup it and protect it.


# Docker Swarm

Docker swarm is an awesome concept that treats multiple docker daemons as one. This means you can horizontally scale your cluster without worrying about many changes in your DevOps progress.

Docker swarm is another RESTful service layer which has exact same endpoints as Docker Daemon does. It has [builtin strategies](https://docs.docker.com/swarm/scheduler/strategy/) (like spread) to pick the actual node (server) to use.

Docker engine 100% works with docker swarm service. Docker swarm is transparent to any docker engine.

## Is it completely transparent?

No. There are still some configuration needed to make things work like network, volume mapping etc. With help of [Docker compose](#docker-compose), the swarm can be configured easily. See [here](https://docs.docker.com/compose/swarm/) for limitations with Docker Swarm.

## Discovery

Docker swarm depends on a discovery service which itself could be a docker service.
The swarm network and discovery service is de-centralised and clustered which means better availability.
Using `swarm join` will regiser itself to discovery service and swarm manager can then collect information from the discovery service.
It is able to use:
* token proto: for non production
* etcd
* consul
and some other key-value stores.

## Swarm Manager TLS

As docker swarm manager needs to actively manage swarm agents, it needs have its own TLS certificates signed by same CA. Otherwise, swarm manager will not be able to talk to swarm agents.
For example:
```bash
docker run -d -p 3376:3376 -t -v /var/lib/boot2docker:/certs:ro swarm manage -H 0.0.0.0:3376 --tlsverify --tlscacert=/certs/ca.pem --tlscert=/certs/server.pem --tlskey=/certs/server-key.pem token://123456789
```
