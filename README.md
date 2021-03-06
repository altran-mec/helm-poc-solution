# Deploy a solution using Helm or Helmfile + day0 configuration


## Solution Architecture

It contains 5 components:
* ingress - nginx ingress 
* portal - frontend (Angular)
* backend - backend service, serving REST endpoints for a frontend (Java - Spring Boot)
* postgres - PostgreSQL database
* adminer - admin ui for database

![Simple Architecture Diagram](https://github.com/altran-mec/helm-poc-multiple-charts/blob/main/solution.PNG)


## Prerequisites
You must install and configure the following tools before moving forward

* minikube
* kubectl
* helm
* helmfile


Next, we need to run another command to enable *Ingress* addon:
```bash
$ minikube addons enable ingress
```

### Edit hosts file
You need to config your **hosts** file - add following lines:

```
<MINIKUBE_IP>	kanban.k8s.com
<MINIKUBE_IP>	adminer.k8s.com
```

A value for `<MINIKUBE_IP>` placeholder is individual per machine. To figure it out you need to have `minikube` up and running. If you're sure it's working you can run the command:
```bash
$ minikube ip
172.17.0.2
```

So in my case, I need to add following lines to the *hosts*  file:
```
172.17.0.2	kanban.k8s.com
172.17.0.2	adminer.k8s.com
```

Location of *hosts* file on different OS:
* [Linux (Ubuntu)](http://manpages.ubuntu.com/manpages/trusty/man5/hosts.5.html)
* [Windows 10](https://www.groovypost.com/howto/edit-hosts-file-windows-10/)
* [Mac](https://www.imore.com/how-edit-your-macs-hosts-file-and-why-you-would-want#page1)

## Create Helm releases

Here is the list of commands that needs to be executed to deploy applications on the cluster. 

### postgres

```bash
$ cd postgres
$ helm install postgres -f values.yaml bitnami/postgresql
```

### backend

```bash
$ cd backend
$ helm install -f values.yaml backend .
```

### portal

```bash
$ cd portal
$ helm install -f values.yaml portal .
```

### adminer

```bash
$ cd adminer
$ helm install -f values.yaml adminer .
```

### ingress

```bash
$ cd ingress
$ helm install -f values.yaml ingress .
```

## Create Helm releases via Helmfile

First you need to add repository defined in `helmfile.yaml`:

```bash
$ helmfile repos
```

Then run `helmfile sync` command to deploy all apps (Helm releases defined in `helmfile.yaml`):

```bash
$ helmfile sync
```
## Testing

Test the application via

http://kanban.k8s.com

http://adminer.k8s.com
