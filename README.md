# Deploy multiple applications using Helm 


## Project architecture

It contains 3 components:
* postgres - database
* kanban-app - backend service, serving REST endpoints for a frontend
* kanban-ui - frontend service

And here is a simpliefied schema of what I would like to achieve:

![Simple Architecture Diagram](https://github.com/wkrzywiec/k8s-helm-helmfile/blob/master/assets/arch-simple.png)


On it you there is an additional component - adminer. It's GUI application for managing the database.

A full picture of Kubernetes cluster that is created with each approach is presented below:

![Kubernetes Objects Architecture](https://github.com/wkrzywiec/k8s-helm-helmfile/blob/master/assets/arch-k8s.png)

## Prerequisites

Before testing any of described approaches you need first go through following steps:

### Install Minikube & Kubectl
* Installing Docker - https://docs.docker.com/install/
* Installing minikube - https://kubernetes.io/docs/tasks/tools/install-minikube/
* Installing kubectl - https://kubernetes.io/docs/tasks/tools/install-kubectl/

### Start a new Minikube cluster

In order to run a minikube cluster:
```bash
$ minikube start
😄  minikube v1.8.1 on Ubuntu 18.04
✨  Automatically selected the docker driver
🔥  Creating Kubernetes in docker container with (CPUs=2) (8 available), Memory=2200MB (7826MB available) ...
🐳  Preparing Kubernetes v1.17.3 on Docker 19.03.2 ...
    ▪ kubeadm.pod-network-cidr=10.244.0.0/16
❌  Unable to load cached images: loading cached images: stat /home/wojtek/.minikube/cache/images/k8s.gcr.io/kube-proxy_v1.17.3: no such file or directory
🚀  Launching Kubernetes ... 
🌟  Enabling addons: default-storageclass, storage-provisioner
⌛  Waiting for cluster to come online ...
🏄  Done! kubectl is now configured to use "minikube"
```

To check the status of the cluster:
```bash
$ minikube status
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

To check that `kubectl` is properly configured:
```bash
$ kubectl cluster-info
Kubernetes master is running at https://127.0.0.1:32768
KubeDNS is running at https://127.0.0.1:32768/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

Next, we need to run another command to enable *Ingress* addon:
```bash
$ minikube addons enable ingress
🌟  The 'ingress' addon is enabled
```

### Edit hosts file
As I want to have two different URLs to enter the *adminer* (database management tool) and *kanban* app you need to config your **hosts** file - add following lines:

```
<MINIKUBE_IP>	adminer.k8s.com
<MINIKUBE_IP>	kanban.k8s.com
```

A value for `<MINIKUBE_IP>` placeholder is individual per machine. To figure it out you need to have `minikube` up and running. If you're sure it's working you can run the command:
```bash
$ minikube ip
172.17.0.2
```

So in my case, I need to add following lines to the *hosts*  file:
```
172.17.0.2	adminer.k8s.com
172.17.0.2	kanban.k8s.com
```

Location of *hosts* file on different OS:
* [Linux (Ubuntu)](http://manpages.ubuntu.com/manpages/trusty/man5/hosts.5.html)
* [Windows 10](https://www.groovypost.com/howto/edit-hosts-file-windows-10/)
* [Mac](https://www.imore.com/how-edit-your-macs-hosts-file-and-why-you-would-want#page1)

## Create helm releases

Here is the list of commands that needs to be executed to deploy applications on the cluster. 

### Postgres

```bash
$ helm install -f kanban-postgres.yaml postgres ./postgres
NAME: postgres
LAST DEPLOYED: Fri Apr 10 22:42:44 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

### Adminer

```bash
$ helm install -f adminer.yaml adminer ./app
NAME: adminer
LAST DEPLOYED: Fri Apr 10 22:43:19 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

### Kanban-app

```bash
$ helm install -f kanban-app.yaml kanban-app ./app
NAME: kanban-app
LAST DEPLOYED: Fri Apr 10 22:43:45 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

### Kanban-ui

```bash
$ helm install -f kanban-ui.yaml kanban-ui ./app
NAME: kanban-ui
LAST DEPLOYED: Fri Apr 10 22:44:16 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

### Ingress

```bash
$ helm install -f ingress.yaml ingress ./ingress
NAME: ingress
LAST DEPLOYED: Fri Apr 10 22:44:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

## Maintanance

In order to check the list of Helm releases:

```bash
$ helm list
NAME      	NAMESPACE	REVISION	UPDATED             STATUS  	CHART         	APP VERSION
adminer   	default  	1       	2020-04-10 22:43:19	deployed	app-0.1.0     	1.16.0     
ingress   	default  	1       	2020-04-10 22:44:42	deployed	ingress-0.1.0 	1.16.0     
kanban-app	default  	1       	2020-04-10 22:43:45	deployed	app-0.1.0     	1.16.0     
kanban-ui 	default  	1       	2020-04-10 22:44:16 deployed	app-0.1.0     	1.16.0     
postgres  	default  	1       	2020-04-10 22:42:44	deployed	postgres-0.1.0	1.16.0 
```

