title: Docker multi-hosting network quick note
date: 2016-03-30 18:24:17
tags:
  - docker
categories:
  - tech
---
To create an `overlay` network on multiple hosts over swarm, following are required:
* a key-value store service: this is used for broadcasting hosts / swarm agents. It can be same kv store swarm used for discovery
* Run docker daemon with following parameters:
  - cluster-store: where the store is
  - cluster-advertise: what network interface to be advertised

# Setup consul KV store
```
docker run -d \
    -p "8500:8500" \
    -h "consul" \
    --restart=unless-stopped \
    --name="kv_store"\
    progrium/consul -server -bootstrap
```

# Daemon Options
```
--engine-opt="cluster-store=consul://$(docker-machine ip mh-keystore):8500"
--engine-opt="cluster-advertise=eth1:2376"
```
**All** swarm-agents should have these options otherwise it will be likely get this error:
```
Error response from daemon: 500 Internal Server Error: failed to parse pool request for address space "GlobalDefault" pool "" subpool "": cannot find address space GlobalDefault (most likely the backing datastore is not configured)
```

# Create overlay network
If using docker-compose, there is nothing need to do. As docker-compose will automatically create defaul network if:
* Single host: it will create a bridge
* Multiple host: it will create a overlay

Once docker-compose file finished, just run `docker-compose up -d` which will create network correspondingly.

otherwise simply use following command at your `swarm`:
```
docker network create --driver overlay --subnet=10.0.9.0/24 my-net
```

# Docker-compose build on Swarm
There is limitation for docker-compose build as it cannot find the target node to build the image.
The only way currently is to build on the node and tag it rather than on swarm.
```
docker build -t <tag name> path/to/dockerfile
```
