title: "How to Run Minishift On MacOSX"
date: 2018-05-18 18:40:23
categories:
  - Openshift
tags:
  - openshift
  - minishift
---

[Minishift](https://github.com/minishift/minishift) allows running a mini-version of Openshift cluster locally for convenience of development and debug. This blog is going through how to run minishift on MacOS.
<!--more-->

# Step1 -- Download latest Minishift

Goto [Mnishift Github Release Page](https://github.com/minishift/minishift/releases), it is able to see the binary assets of Minishift for different operation systems. By writing this article, the latest versino of Minishift for MacOS is [minishift-1.17.0-darwin-amd64.tgz](https://github.com/minishift/minishift/releases/download/v1.17.0/minishift-1.17.0-darwin-amd64.tgz).

Once downloaded, expand the tar file and copy the `minishift` file to `/usr/local/bin` folder

```bash
# untar the downloaded file
tar -xvf ~/Downloads/<downloaded file>

sudo cp ~/Downloads/<expanded folder>/minishift /usr/local/bin

sudo chmod +x /usr/local/bin/minishift
```

# Step2 -- Install dependencies / Virtualization Environment

Before Minishift can run, we need setup the virualization environment which supports Openshift running under MacOS environment.

When doing this step, I assum you have [brew](https://brew.sh/) installed /configured correctly in your computer.

You can simply copy and paste code below and run in a terminal.

```bash
# Update brew first
brew update
# install docker-machine-driver-xhyve
brew install docker-machine-driver-xhyve
# Set the owner User ID (SUID) for the binary 
sudo chmod u+s $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
```

# Step 3 -- Run minishift

Once setup virtualization environment successfully, let's start minishift:

```bash
minishift start
```

For first time running, it will download all related dependencies including images / cli binary .

After starting successfully, it is able to see the allocated IP address to the master node of the openshift cluster. In my case it is `192.168.64.2`.

Then you should be able to open `https://192.168.64.2:8443` in your browser and login with username and password both as `admin`.

To shutdown your minishift cluster simply run:

```bash
minishift stop
```

