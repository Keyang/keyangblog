title: run docker in docker
date: 2016-04-07 17:32:14
tags:
  - docker
categories:
  - tech
---
Run docker in docker
```
docker run --privileged -i -t -d --restart=unless-stopped -p 2376:2376 -p 10000-11000:10000-11000 -p 10000-11000:10000-11000/udp -v /mnt/opt:/opt:rw -v /etc/docker:/etc/docker:ro -v /mnt/var/lib/docker:/var/lib/docker --name=docker docker:dind  -H tcp://0.0.0.0:2376 --storage-driver=aufs --tlsverify --tlscacert /etc/docker/ca.pem --tlscert /etc/docker/server.pem --tlskey /etc/docker/server-key.pem
```
