---
title: "Pod"
description: "A Pod is the smallest and simplest unit you can deploy. It represents a single instance of a running process in a cluster. A Pod may contain one or more tightly coupled containers, sharing the same network namespace and having access to the same storage volumes."
date: 2023-07-16T09:05:58+02:00
lastmod: 2023-07-16T09:05:58+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resources"
weight: 1005
toc: false
---
A Pod is the smallest and simplest unit you can deploy. It represents a single instance of a running process in a cluster. A Pod may contain one or more tightly coupled containers, sharing the same network namespace and having access to the same storage volumes. To make it simple, let's use an analogy: think of a Pod as a small, self-contained lunchbox. Inside this lunchbox, you can put different food items (containers) like a sandwich, a juice box, and a fruit cup. These items are meant to be eaten together, and you carry them in the same lunchbox.

Similarly, a Pod in Kubernetes holds containers that are designed to work together as a single unit. For example, if you have a web application, you might put the web server and the application server in the same Pod because they need to communicate with each other.

The containers inside a Pod share the same network IP address and port space, which means they can easily communicate with each other using "localhost." They also share the same lifecycle, so if the Pod is created, all containers inside it start running, and if the Pod is deleted, all the containers are terminated together.

```yaml
# pod-definition.yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```
## Commands
Create a resource from a file
```shell
kubectl create -f pod-definition.yaml
```
Apply a configuration to a pod by file name
```shell
kubectl apply -f pod-definition.yaml
```
Display one or many pods.
```shell
kubectl get pods
```
Prints a table of the most important information about the specified resources.
```shell
kubectl describe pod myapp-pod
```
Quickly generate a new pod definition 
```shell
kubectl run <pod-name> --image nginx --dry-run=client -o yaml > pod-definition.yaml
```
Generate a pod definition out of an exisiting pod
```shell
kubectl get pod <pod-name> -o yaml > pod-definition.yaml
```