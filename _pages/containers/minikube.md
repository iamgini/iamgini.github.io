---
layout: post
title: minikube
author: gini
categories: [ kubernetes, minikube ]
image: "assets/images/2024/minikube-main.png"
tags: []
show-avatar: false
permalink: minikube
featured: false
hidden: false
titleshort: minikube
---

## Introduction

Minikube is a free, open-source tool that lets you run a single-node Kubernetes cluster locally on your laptop or desktop machine. This makes it a fantastic option for:

- Learning Kubernetes: Minikube provides a lightweight and easy-to-use environment to experiment with Kubernetes concepts, deploy containerized applications, and explore its functionalities.
- Development & Testing: Developers can use minikube to test and debug their containerized applications locally before deploying them to production environments.
- Quick Demos & Prototyping: Need to show off a Kubernetes concept or prototype an application? Minikube's simplicity allows you to set up a quick and disposable cluster for demonstrations.

- [Introduction](#introduction)
    - [Install minikube](#install-minikube)
- [Just to test ? Use this Lab](#just-to-test--use-this-lab)
- [Deploy minikube](#deploy-minikube)
  - [Setup Docker or Virtualization](#setup-docker-or-virtualization)
  - [Install and Configure kubectl](#install-and-configure-kubectl)
  - [Download and Install minikube](#download-and-install-minikube)
  - [Configure to Run docker without `sudo`](#configure-to-run-docker-without-sudo)
  - [Start minikube](#start-minikube)
    - [Start minikube cluster](#start-minikube-cluster)
    - [Verify cluster information](#verify-cluster-information)
  - [Enable Kubernetes metrics-server](#enable-kubernetes-metrics-server)
  - [Kubernetes Dashboard](#kubernetes-dashboard)
    - [If you are running minikube inside a VM](#if-you-are-running-minikube-inside-a-vm)
  - [Get your hands dirty !](#get-your-hands-dirty-)
    - [If you are on Remote VM](#if-you-are-on-remote-vm)
  - [LoadBalancer deployments](#loadbalancer-deployments)
  - [Multi-node Kubernetes Cluster using minikubeMulti-Node](#multi-node-kubernetes-cluster-using-minikubemulti-node)
  - [Finished Testing ?](#finished-testing-)
  - [Using Docker or Podman with minikube](#using-docker-or-podman-with-minikube)
- [Troubleshooting](#troubleshooting)
  - [`minikube start` exits with error on `GUEST_MISSING_CONNTRACK`](#minikube-start-exits-with-error-on-guest_missing_conntrack)
- [References](#references)



## Install minikube

```shell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

**Note: Before you start minikube cluster**

minikube will need a hypervisor (VirtualBox, Hyperkit or KVM) but if you are already inside a virtual machine (and nested virtualization is not possible), then it is possible to skip the creation of an additional VM layer by using the none driver. But please note, you need to install and setup Docker or Podman environment in that vm.

Since `vm-driver` is deprecated, use `driver` option

```shell
$ minikube config set driver podman
❗  These changes will take effect upon a minikube delete and then a minikube start
```

To make none the default driver:

```shell
$ sudo minikube config set driver none
❗  These changes will take effect upon a minikube delete and then a minikube start
```

Set container runtime

```shell
$ minikube config set container-runtime containerd
❗  These changes will take effect upon a minikube delete and then a minikube start
```
You can also mention the kubernetes version you need to install.

```shell
$ minikube start --kubernetes-version v1.16.0
## or
$ minikube start \
  driver=docker
  --wait=false \
  --kubernetes-version=v1.18.3

## start minikube with ingress and flannel cni
$ minikube start --addons=ingress --cni=flannel --install-addons=true \
    --kubernetes-version=stable \
    --driver=docker --wait=false \
    --cpus=4 --memory=6g
```

You have many other useful arguments to pass to control the minikube cluster configurations.

```shell
$ minikube config set driver none
$ minikube config set cpus 2
$ minikube config set memory 4096
$ minikube config defaults kubernetes-version v1.30.0
$ minikube start --wait=false --kubernetes-version=v1.23.1
```

Check minikube capacity

```shell
$ kubectl get node minikube -o jsonpath='{.status.capacity}'
```

# Just to test ? Use this Lab

There is a simple lab to see and test minikube; use it **[here](https://kubernetes.io/docs/tutorials/hello-minikube/#create-a-minikube-cluster)**


# Deploy minikube

## Setup Docker or Virtualization

We are using docker here instead of Virtualization (VirtualBox etc.). Hence we need to install docker for the same.

Below steps are for Debian and you can refer [documentation](https://docs.docker.com/engine/install/) for other operating systems.(or [Install Docker on Debian](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-debian-9))

```shell
## remove any existing package
$ sudo apt-get remove docker docker-engine docker.io containerd runc

## Update the apt package index and install packages
## to allow apt to use a repository over HTTPS:
$ sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

## Add Docker’s official GPG key:
$ curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

$  echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

## Install docker and tools
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

## Install and Configure kubectl

You need `kubectl` to manage your cluster resources.

```
## Download kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

## Download the kubectl checksum file and Validate
$ curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
$ echo "$(<kubectl.sha256) kubectl" | sha256sum --check

## Install
$ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

*Note: if you have issue with `sudo` access or other thing try below.*

```shell
# Make the kubectl binary executable.
chmod +x ./kubectl
# Move the binary in to your PATH.
sudo mv ./kubectl /usr/local/bin/kubectl
```

**Test to ensure the version you installed is up-to-date:**

```shell
kubectl version --client
```

## Download and Install minikube

```
# download and install package
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

$ sudo install minikube-linux-amd64 /usr/local/bin/minikube

# check version
$ minikube version
minikube version: v1.21.0
commit: 76d74191d82c47883dc7e1319ef7cebd3e00ee11
```

## Configure to Run docker without `sudo`

Add your user to the `docker` group:

```shell
$ sudo usermod -aG docker $USER && newgrp docker
```

## Start minikube


### Start minikube cluster

```
$ sudo minikube start -driver=none --wait=false
😄  minikube v1.5.2 on Debian 9.11
🤹  Running on localhost (CPUs=2, Memory=7483MB, Disk=10013MB) ...
ℹ️   OS release is Debian GNU/Linux 9 (stretch)
⚠️  VM may be unable to resolve external DNS records
🐳  Preparing Kubernetes v1.16.2 on Docker '19.03.5' ...
💾  Downloading kubelet v1.16.2
💾  Downloading kubeadm v1.16.2
🚜  Pulling images ...
🚀  Launching Kubernetes ...
🤹  Configuring local host environment ...

⚠️  The 'none' driver provides limited isolation and may reduce system security and reliability.
⚠️  For more information, see:
👉  https://minikube.sigs.k8s.io/docs/reference/drivers/none/

⚠️  kubectl and minikube configuration will be stored in /root
⚠️  To use kubectl or minikube commands as your own user, you may need to relocate them. For example, to overwrite your own settings, run:

    ▪ sudo mv /root/.kube /root/.minikube $HOME
    ▪ sudo chown -R $USER $HOME/.kube $HOME/.minikube

💡  This can also be done automatically by setting the env var CHANGE_MINIKUBE_NONE_USER=true
⌛  Waiting for: apiserver
🏄  Done! kubectl is now configured to use "minikube"
💡  For best results, install kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl/

```

Now we have a running kubernetes cluster and let us see the status.

### Verify cluster information

```
$ kubectl cluster-info

$ kubectl get nodes
NAME       STATUS   ROLES                  AGE   VERSION
minikube   Ready    control-plane,master   15m   v1.20.7
```
## Enable Kubernetes metrics-server

```shell
$ minikube addons enable metrics-server
    ▪ Using image k8s.gcr.io/metrics-server/metrics-server:v0.4.2
🌟  The 'metrics-server' addon is enabled
```

## Kubernetes Dashboard

You can enable the dashboard as below. (Take another console to run it)

```
$ sudo minikube dashboard
🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
http://127.0.0.1:39067/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```

Usually `minikube` will open the browser with the dashboard URL if you are with GUI. Access the url and check.

### If you are running minikube inside a VM

Then accessing Dashboard from host machine (or from your laptop) is bit tricky. By default minikube dashboard will exposed as `localhost:PORT`.

In above example, I have deployed the minikube inside a GCP (Google Cloud Platform) instance and see below how I am accessing the Dashboard.

**on minikube VM**

```
##
$ minikube dashboard
🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
🎉  Opening http://127.0.0.1:36959/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
👉  http://127.0.0.1:36959/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```

**On my Laptop/Workstation**

```shell
## Open an SSH tunnel
## ssh -L LOCAL_PORT:localhost:REMOTE_PORT user@REMOTE_VM_IP
$ ssh -L 36959:localhost:36959 gini@123.123.234.234
.
.
gini@minikube-vm:~$
```

Open a browser and access the same url (output of `minikube dashboard` command)

`http://127.0.0.1:36959/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/`

Bingo !

## Get your hands dirty !

```shell
## Deploy an app
$ kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
$ kubectl expose deployment hello-minikube --type=NodePort --port=8080

$ kubectl get services hello-minikube

## let minikube open the browser
$ minikube service hello-minikube

## Alternatively, use kubectl to forward the port:
kubectl port-forward service/hello-minikube 7080:8080
```

### If you are on Remote VM

```shell
$ ssh -L 7080:localhost:7080 gini@123.123.234.234
```
And access the url fromlocal  browser - `localhost:7080`

## LoadBalancer deployments

To access a LoadBalancer deployment, use the “minikube tunnel” command. Here is an example deployment:

```shell
kubectl create deployment balanced --image=k8s.gcr.io/echoserver:1.4
kubectl expose deployment balanced --type=LoadBalancer --port=8080


## In another window, start the tunnel to create a routable IP for the ‘balanced’ deployment:

minikube tunnel
To find the routable IP, run this command and examine the EXTERNAL-IP column:

kubectl get services balanced
```

Your deployment is now available at <EXTERNAL-IP>:8080

## Multi-node Kubernetes Cluster using minikube

```shell

$ minikube start \
  --driver=virtualbox \
  --nodes 3 \
  --cni calico \
  --cpus=2 \
  --memory=2g \
  --kubernetes-version=v1.30.0 \
  --container-runtime=containerd

$ kubectl get nodes
NAME           STATUS   ROLES           AGE     VERSION
minikube       Ready    control-plane   3m28s   v1.30.0
minikube-m02   Ready    <none>          2m29s   v1.30.0
minikube-m03   Ready    <none>          91s     v1.30.0
```

NOTE: Make sure, a CNI is installed and configured before adding the node. You can use any supported CNI or use `--cni calico` while initiating a `minikube` cluster.

## Create multi-master minikube

```shell
$ minikube start \
  --driver=virtualbox \
  --nodes 5 \
  --ha \
  --cni calico \
  --cpus=2 \
  --memory=2g \
  --kubernetes-version=v1.30.0 \
  --container-runtime=containerd


$ kubectl get nodes
NAME           STATUS   ROLES           AGE     VERSION
minikube       Ready    control-plane   6m28s   v1.30.0
minikube-m02   Ready    control-plane   4m36s   v1.30.0
minikube-m03   Ready    control-plane   2m45s   v1.30.0
minikube-m04   Ready    <none>          112s    v1.30.0
minikube-m05   Ready    <none>          62s     v1.30.0
```

Then use `minikube node add` to add nodes to an existing cluster.

```shell

```

Or using podman

By default, `minikube` executes Podman with sudo. To use Podman without sudo, set the rootless property to true:

```shell
$ minikube config set rootless true
```

```shell
$ minikube start \
    --driver=podman \
    --nodes 5 \
    --ha \
    --cni calico \
    --cpus=2 \
    --memory=2g \
    --kubernetes-version=v1.33.0 \
    --container-runtime=containerd \
    --profile k8s-133-podman
```

## Finished Testing ?

```shell
## Pause Kubernetes without impacting deployed applications
$ minikube pause

## Halt the cluster:
$ minikube stop
```

## Using Docker or Podman with minikube


```
# Tell Docker CLI to talk to minikube's VM
eval $(minikube -p minikube docker-env)
# or eval $(minikube docker-env)

# Tell Docker CLI to talk to minikube's VM
eval $(minikube -p minikube podman-env)

# Save IP to a hostname
echo "`minikube ip` docker.local" | sudo tee -a /etc/hosts > /dev/null

# Test
docker run hello-world
```


# Troubleshooting

## `minikube start` exits with error on `GUEST_MISSING_CONNTRACK`

**Error**
❌  Exiting due to GUEST_MISSING_CONNTRACK: Sorry, Kubernetes 1.20.7 requires conntrack to be installed in root's path

**Solution**

```shell
sudo apt-get install -y conntrack
```

# References

- https://github.com/kubernetes/minikube
- https://minikube.sigs.k8s.io/docs/start/
- [Run Docker without Docker Desktop on macOS](https://dhwaneetbhatt.com/blog/run-docker-without-docker-desktop-on-macos)