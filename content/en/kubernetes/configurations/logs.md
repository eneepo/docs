---
title: "Logs"
description: ""
date: 2023-07-26T07:34:43+02:00
lastmod: 2023-07-26T07:34:43+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1060
toc: true
---

```shell
k logs <pod-name>
```

```yaml
# event-simulator.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: event-simulator
  name: event-simulator
spec:
  containers:
  - image: kodekloud/event-simulator
    name: event-simulator
```

```shell
k logs -f event-simulator-pod
```


```yaml
# event-simulator-2.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: event-simulator
  name: event-simulator-multi-image
spec:
  containers:
  - image: kodekloud/event-simulator
    name: event-simulator-1
  - image: kodekloud/event-simulator
    name: event-simulator-2
```

```shell
k logs -f event-simulator-pod event-simulator-1
```