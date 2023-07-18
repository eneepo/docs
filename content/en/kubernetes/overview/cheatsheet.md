---
title: "Commands Cheatsheet"
description: ""
lead: ""
date: 2023-07-16T09:29:02+02:00
lastmod: 2023-07-16T09:29:02+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "overview"
weight: 100
toc: false
---

# Pod

```shell
# Generate a pod definition out of an exisiting pod
kubectl get pod <pod-name> -o yaml > pod-definition.yaml
# Create a resource from a file
kubectl create -f pod-definition.yaml
# Apply a configuration to a pod by file name
kubectl apply -f pod-definition.yaml
# Display one or many pods.
kubectl get pods
# Prints a table of the most important information about the specified resources.
kubectl describe pod myapp-pod
# Quickly generate a new pod definition 
kubectl run <pod-name> --image nginx --dry-run=client -o yaml > pod-definition.yaml
# Generate a pod definition out of an exisiting pod
kubectl get pod <pod-name> -o yaml > pod-definition.yaml
```