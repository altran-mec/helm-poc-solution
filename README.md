# Deploy multiple applications using Helm 


## Project architecture

It contains 4 components:
* postgres - database
* backend - backend service, serving REST endpoints for a frontend
* portal - frontend service
* ingress - nginx ingress 

And here is a simplified schema of what I would like to achieve:

![Simple Architecture Diagram](https://github.com/altran-mec/helm-poc-multiple-charts/blob/main/helm_multi.PNG)


## Prerequisites
You must install and configure the following tools before moving forward

* minikube
* kubectl
* Helm


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
$ cd postgres
$ helm install -f values.yaml postgres .
```

### Adminer

```bash
$ cd adminer
$ helm install -f values.yaml adminer .
```

### backend

```bash
$ cd backend
$ helm install -f values.yaml backend .
```

### portal

```bash
$ helm install -f values.yaml portal .
```

### Ingress

```bash
$ helm install -f values.yaml ingress .
```

## Maintanance

In order to check the list of Helm releases:

```bash
$ helm list
```
