# ENSF 400 - Assignment 3: Kubernetes Deployment with Canary Strategy

## Introduction

This document provides a detailed account of the methodology employed to establish a canary deployment on Kubernetes, utilizing Minikube. It encompasses verbatim terminal outputs to substantiate each action undertaken in the preparation, deployment, and validation phases of the application.

## Prerequisites

- Minikube 
- kubectl command-line tool

## Steps Taken

### Starting Minikube

Initializing Minikube, establishing a single-node Kubernetes cluster:

```
$ minikube start
```

#### Output

```
😄  minikube v1.32.0 on Ubuntu 20.04 (docker/amd64)
✨  Automatically selected the docker driver. Other choices: none, ssh
📌  Using Docker driver with root privileges
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.28.3 preload ...
    > preloaded-images-k8s-v18-v1...:  403.35 MiB / 403.35 MiB  100.00% 153.40 
    > gcr.io/k8s-minikube/kicbase...:  453.90 MiB / 453.90 MiB  100.00% 37.90 M
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.28.3 on Docker 24.0.7 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🔎  Verifying Kubernetes components...
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

### Enabling Ingress Addon

Enabling the Ingress addon for managing external access to services:

```
$ minikube addons enable ingress
```

#### Output

```
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ minikube addons enable ingress
💡  ingress is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20231011-8b53cabe0
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20231011-8b53cabe0
    ▪ Using image registry.k8s.io/ingress-nginx/controller:v1.9.4
🔎  Verifying ingress addon...
🌟  The 'ingress' addon is enabled
```

### Deploying nginx and Application Components

Applying the Kubernetes manifest files for the nginx reverse proxy and both versions of the application:


```
@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f nginx-dep.yaml
deployment.apps/nginx-dep created
@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f nginx-configmap.yaml
configmap/nginx-configmap created
@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f nginx-svc.yaml
service/nginx-svc created
@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-1-dep.yaml
deployment.apps/app-1-dep created
@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-1-svc.yaml
service/app-1-svc created
@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $  kubectl apply -f app-2-dep.yaml
deployment.apps/app-2-dep created
@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-2-svc.yaml
service/app-2-svc created
@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-1-ingress.yaml
ingress.networking.k8s.io/app-1-ingress created
@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-2-ingress.yaml
ingress.networking.k8s.io/app-2-ingress created
```

### Verifying Deployment with Minikube IP

Retriving the IP address of Minikube and testing the application for confirming the canary deployment was functioning correctly:

```
$ minikube ip
```

#### Output

```
192.168.49.2
```

Testing the responses of the application:

```
$ curl http://192.168.49.2/
```

#### Output

```
Hello World from [app-1-dep-86f67f4f87-c2c7l]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-2-dep-7f686c4d8d-crkq4]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-2-dep-7f686c4d8d-crkq4]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-c2c7l]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-c2c7l]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-c2c7l]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-c2c7l]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-c2c7l]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-c2c7l]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-2-dep-7f686c4d8d-crkq4]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-c2c7l]!@FardinAryan7 ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ 
...
```

## Conclusion

The application has been successfully deployed and is now accessible. The effectiveness of the canary deployment strategy has been confirmed, as traffic is being correctly distributed between the stable and canary versions of the application.
