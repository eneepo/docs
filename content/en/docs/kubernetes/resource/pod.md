---
title: "Pod"
description: ""
lead: ""
date: 2023-07-09T16:53:32+02:00
lastmod: 2023-07-09T16:53:32+02:00
draft: true
images: []
menu:
  docs:
    parent: ""
    identifier: "pod-5006c9ca5bcf578ecdaef7de898942c1"
weight: 999
toc: true
---

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
```shell
k create -f pod-definition.yaml
k apply -f pod-definition.yaml
k get pods
k describe pod myapp-pod
```
Advanced commands
```shell
k run <pod-name> --image nginx --dry-run=client -o yaml
k get pod <pod-name> -o yaml > pod-definition.yaml
k pod <pod-name>
```