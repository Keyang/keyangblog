title: "Run Nginx as reverse proxy on Openshift"
date: 2018-06-01 17:32:24
categories:
  - Openshift
tags:
  - openshift
  - nginx
---

In this post, I will introduce a way to deploy a Nginx Reverse Proxy to Openshift base on [RedHat certified Nginx Builder Image](https://access.redhat.com/containers/?tab=overview&platform=openshift#/registry.access.redhat.com/rhscl/nginx-112-rhel7).

<!--more-->

In my previous post: [How to deploy website to openshift with nginx](http://keyangxiang.com/2017/10/24/Openshift/How-to-run-nginx-on-openshift-and-deploy-website/), I simply introduced how to use the Nginx Builder Image to deploy a website on Openshift. 

Reverse Proxy configuration, however, is not part of web content. Thus it is not able to directly to use the Builder Image to setup the config and we will need create a real Nginx Image which can be futher configured with reverse proxy table.

![image](https://www.plantuml.com/plantuml/png/oyjFILLGSYhDp4b9BLBmIy_Cgr3mp4rCJrLII2nMICfKuCe3gu38E2KMfo99vu44KHG4fPQb5WKKvLTKWXKKW5WAN4cPkdPGjI1IrmuipiK50000)


# Step 1 -- Create Nginx base image

We are creating the Nginx Base Image based on [RedHat certified Nginx Builder Image](https://access.redhat.com/containers/?tab=overview&platform=openshift#/registry.access.redhat.com/rhscl/nginx-112-rhel7) (rhscl/nginx-112-rhel7). 

First, let's create an app that has an empty `index.html` page from local directory (e.g. `$HOME/webfolder`):

```sh
oc new-app registry.access.redhat.com/rhscl/nginx-112-rhel7~$HOME/webfolder --name=nginxbase
```

This will create corresponding resources like `buildconfig`, `deploymentconfig`, `imagestream`, `service` etc for `nginxbase` in Openshift.

Once resources created successfully, let's build the image

```sh
oc start-build nginxbase --from-dir=$HOME/webfolder
```

This will create the Nginx Base image and push to registry in Openshift.

Once the `nginxbase` image being created, we could remove all other resources:

```sh
oc delete bc/nginxbase
oc delete dc/nginxbase
oc delete svc/nginxbase
```

# Step 2 -- Add reverse proxy configuration

We will need create a custom image for reverse proxy. First, create an empty folder and add following files:

* Dockerfile
* nginx-proxy.conf: the proxy definition file

In `Dockerfile`, add following content:

```dockerfile
FROM nginxbase:latest
ADD nginx-proxy.conf /opt/app-root/etc/nginx.default.d/nginx-proxy.conf
```

When nginx server starts, it will automatically load configuration files `*.conf` in `/opt/app-root/etc/nginx.default.d` folder into its default server definition closure. So we add our `nginx-proxy` configuration file into that folder.

In `nginx-proxy.conf` file, we could add reverse proxy configuration. For example:

```
location /userProfile {
  proxy_set_header  Host $host;
  proxy_set_header  X-Real-IP $remote_addr;
  proxy_set_header  X-Forwarded-Proto https;
  proxy_set_header  X-Forwarded-For $remote_addr;
  proxy_set_header  X-Forwarded-Host $remote_addr;
  proxy_pass http://userProfile:8080;
}

location /uploadAsset {
  proxy_set_header  Host $host;
  proxy_set_header  X-Real-IP $remote_addr;
  proxy_set_header  X-Forwarded-Proto https;
  proxy_set_header  X-Forwarded-For $remote_addr;
  proxy_set_header  X-Forwarded-Host $remote_addr;
  proxy_buffering off;
  client_max_body_size 50m;
  proxy_pass http://uploadAsset:8080;
}
```

# Step 3 -- Create Reverse Proxy

Next, create reverse proxy app in Openshift:

```sh
oc new-app --strategy=docker nginxbase~<path to dockerfile> --name=myReverseProxy
```

Just be aware we have to use `docker strategy` as app's build strategy so that Openshift knows that we will upload a Docker project.

Start Build:

```sh
oc start-build myReverseProxy --from-dir=<path to dockerfile>
```

Once the build finished, Openshift should automatically deploy a new pod up and running. We could expose the reverse proxy by running

```sh
oc expose svc/myReverseProxy
```

and access the nginx reverse proxy through the routes created.

