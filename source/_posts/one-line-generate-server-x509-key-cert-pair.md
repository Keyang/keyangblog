title: one line generate server x509 key/cert pair
date: 2017-07-13 13:20:08
tags:
---

```
openssl req -nodes -new -x509 -keyout key.pem -out cert.pem -days 365
```