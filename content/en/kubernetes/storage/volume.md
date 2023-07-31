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

In Kubernetes, various volume types are available to provide different storage options for pods. The choice of volume type depends on the underlying infrastructure, the storage requirements of your applications, and the level of data persistence needed. Here are some common volume types used in Kubernetes:

## Common Volume Types
### EmptyDir
  - An EmptyDir volume is a temporary and non-persistent volume that exists only for the lifetime of a pod.
  - It is useful for sharing files between containers within the same pod or for storing temporary data that can be discarded when the pod is deleted or restarted.

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

### HostPath
  - A HostPath volume mounts a file or directory from the node's filesystem directly into the pod.
  - It is suitable for cases where you need to access files or data that reside on the node's filesystem.
  - Caution: Data stored on the node's filesystem is not replicated or shared across nodes and is not suitable for production use.

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

### PersistentVolumeClaim (PVC)
  - A PVC represents a request for a specific amount of storage and access mode from a Persistent Volume (PV).
  - PVs are provisioned independently of pods, and PVCs act as a request to bind a PV to a pod based on its storage requirements.
  - This is a more permanent and durable option for storing data that persists even if the pod is rescheduled or deleted.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: slow
```

### NFS
  - An NFS (Network File System) volume allows you to mount a remote NFS share into a pod.
  - NFS volumes are widely used for sharing data across nodes in a cluster or across different clusters.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
    - name: app-container
      image: your_app_image
      volumeMounts:
        - name: nfs-volume
          mountPath: /data
  volumes:
    - name: nfs-volume
      nfs:
        server: nfs-server.example.com
        path: /exported/path
```

### ConfigMap and Secret
  - ConfigMap and Secret volumes are special volumes used to expose ConfigMaps and Secrets as files inside the pod.
  - They allow you to inject configuration data and sensitive information (e.g., passwords, API keys) into pods without modifying the pod's YAML definition.

#### `configMap`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
    - name: app-container
      image: your_app_image
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: example-configmap
```
#### `secret`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
    - name: app-container
      image: your_app_image
      volumeMounts:
        - name: secret-volume
          mountPath: /etc/secret
  volumes:
    - name: secret-volume
      secret:
        secretName: example-secret
```

### AWS Elastic Block Store (EBS) and Azure Disk
  - These volume types provide block storage in cloud environments like Amazon Web Services (AWS) and Microsoft Azure, respectively.
  - They offer persistent and durable storage that can be attached to pods running in the cloud.

### GCE Persistent Disk
  - This volume type is specific to Google Cloud Platform (GCP) and provides persistent block storage for pods running on GCP.

### CSI Driver-based Volumes
  - Container Storage Interface (CSI) allows third-party storage providers to develop drivers that enable dynamic provisioning of storage in Kubernetes.
  - CSI volumes can include various storage solutions like Ceph, GlusterFS, and many others.
