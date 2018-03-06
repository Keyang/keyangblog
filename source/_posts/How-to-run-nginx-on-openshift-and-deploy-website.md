title: How to deploy website to openshift with nginx?
date: 2017-10-24 22:04:14
tags:
  - openshift
  - nginx
---

Although Openshift is able to use docker images, it has much more restrictions like no root user. Thus not all docker images can work straight on Openshift. Unfortunately, the official `nginx` docker image does not work on openshift.

In this post, I will simply go through how to run [this](https://access.redhat.com/containers/?tab=overview&platform=openshift#/registry.access.redhat.com/rhscl/nginx-112-rhel7) Official RedHat nginx image on Openshift and deploy a website onto it.

<!--more-->

So, have your [openshift cli tool (oc)](https://developers.openshift.com/managing-your-applications/client-tools.html) ready, let's get started.

# Step 1 - Create App / Deploy website source code

With `oc` tool, create a new app.

```sh
#oc new-app [BuilderImage]~[Source Code Repo]
oc new-app registry.access.redhat.com/rhscl/nginx-112-rhel7~https://github.com/Keyang/keyang.github.io.git --name=myapp
```

The openshift will pull the image from the registry and register it locally as builder image which will allow building images along with source code of website. It creates `build config`. 

Then it will pull source code and `assemble` it with build image to produce another image stream.

This command will also create `deployment config` and `service`.

## Build Local Source

If you have no accessible git repo, it is able to build againt local source.

Slightly different, create a new app on current folder

```sh
#oc new-app [BuilderImage]~[Source Code Repo]
oc new-app nginx-112-rhel7~./ --name=myapp
```

This will not actually upload the source code from current folder to Openshift but just create a build config. Thus we need to start the build with extra parameters:

```sh
oc start-build myapp --from-dir=./
```

with `--from-dir` param, `oc` will upload content of current directory and builder image will `assemble` the code.


# Step 2 - Expose Nginx Server

Once app is created and built, we could expose it through `router`.

```sh
oc expose svc/myapp
```

Once it is exposed, your nginx server and your website should be accessible with the route associated.