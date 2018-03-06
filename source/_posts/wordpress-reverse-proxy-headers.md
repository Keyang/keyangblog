title: wordpress reverse proxy headers
date: 2016-03-17 21:32:31
tags:
  - wordpress
  - nginx

---
When put wordpress blog behind a reverse proxy (e.g. nginx), some headers have to be setup:

```
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header HTTP_X_FORWARDED_PROTO $scheme;

```
