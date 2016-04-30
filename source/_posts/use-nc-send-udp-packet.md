title: use nc send udp packet
date: 2016-03-31 17:09:54
tags:
  - cli
  - linux
categories:
  - tech
---
Netcat (nc) is very powerful tool. Install `apt-get install netcat`
Send UDP packet with netcat:
```
echo "content goes to server" | nc -u <ip> <port>
```
