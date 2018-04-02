title: 'How to install Node.JS locally'
date: 2018-04-01 16:30:12
categories:
  - Node.JS
tags:
  - nodejs
---
Installing Node.JS locally will take no more than 5 minutes. This post will go through the steps very quickly.
<!--more-->

# Download Installer
First, download Node.JS installer according to target operation system from [here](https://nodejs.org/en/download/)

The installer downloaded should be executable on target operation system (e.g. `.msi` on Window and `.pkg` on MacOSX)

# Run Installer

Follow the wizard and install Node.JS to target machine.

# Restart and Test

Once installation finished, open a `bash` or `cmd`. Installation should have both `node` and `npm` commands exposed.

```bash
$ node --version
v6.9.5
$ npm --version
3.10.10
```

It is essential to have **both** commands running correctly.

