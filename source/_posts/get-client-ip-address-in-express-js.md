title: get client ip address in express.js
date: 2016-03-30 13:47:49
tags:
  - node.js
  - express.js

---
According to [this](http://stackoverflow.com/questions/8107856/how-to-determine-a-users-ip-address-in-node), do following to determine the client ipaddress:

```js
var ip = (req.headers['x-forwarded-for'] ||
     req.connection.remoteAddress ||
     req.socket.remoteAddress ||
     req.connection.socket.remoteAddress).split(",")[0];
```
