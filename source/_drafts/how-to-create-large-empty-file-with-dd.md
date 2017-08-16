title: how to create large empty file with dd
tags: linux
---

Use `dd` to create  large file (1000M) under linux:

```
dd if=/dev/zero of=/var/largeFile bs=1M count=1000
```

