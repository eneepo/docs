---
title: "Volume"
description: ""
date: 2023-07-30T20:32:07Z
lastmod: 2023-07-30T20:32:07Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "storage"
weight: 1005
toc: true
---

In Kubernetes, the term "volume" refers to a directory accessible to containers in a pod. Volumes provide a way to store and share data between containers or persist data beyond the lifetime of a pod. There are various types of volumes, such as emptyDir, hostPath, persistentVolumeClaim, etc. Here, I'll provide examples of different volume types using YAML configurations and imperative commands.

## Examples
### EmptyDir Volume (In-Memory Volume)
An EmptyDir volume is created and attached to a pod. It exists as long as the pod is running and is useful for temporary data sharing between containers.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-pod
spec:
  containers:
    - name: container1
      image: nginx
      volumeMounts:
        - name: shared-data
          mountPath: /data
    - name: container2
      image: busybox
      command: ["/bin/sh", "-c", "echo 'Hello from container2' > /data/file.txt"]
      volumeMounts:
        - name: shared-data
          mountPath: /data
  volumes:
    - name: shared-data
      emptyDir: {}
```

## HostPath Volume
A HostPath volume mounts a file or directory from the node's filesystem into the pod. It is not suitable for production environments but can be helpful for testing and development.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostpath-pod
spec:
  containers:
    - name: container1
      image: nginx
      volumeMounts:
        - name: hostpath-volume
          mountPath: /data
  volumes:
    - name: hostpath-volume
      hostPath:
        path: /host/data
        type: DirectoryOrCreate
```
