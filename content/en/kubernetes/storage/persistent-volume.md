---
title: "Persistent Volume"
description: ""
date: 2023-07-30T21:09:24Z
lastmod: 2023-07-30T21:09:24Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "storage"
weight: 1005
toc: true
---

In Kubernetes, a `PersistentVolume` (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. It is a way to decouple storage provision from the lifecycle of pods that use the storage. PVs are used to store data in a durable and independent way, and they exist outside the lifecycle of pods. Pods can claim a PV using a `PersistentVolumeClaim` (PVC), and Kubernetes ensures that the same PV is mounted to the pod if the PVC requests it.

Here's an example of a PersistentVolume YAML file:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: slow
  hostPath:
    path: /data/persistent_volume
```

Explanation of the fields in the YAML file:
- `metadata.name`: The name of the PersistentVolume.
- `spec.capacity.storage`: The size of the volume, in this case, 5 gigabytes.
- `spec.accessModes`: The access modes the volume allows. Multiple modes can be specified (comma-separated), but not all combinations are supported. Common modes include:
  - `ReadWriteOnce`: The volume can be mounted as read-write by a single node.
  - `ReadOnlyMany`: The volume can be mounted read-only by many nodes.
  - `ReadWriteMany`: The volume can be mounted as read-write by many nodes (not supported by all volume types).
- `spec.persistentVolumeReclaimPolicy`: This field specifies what happens to the PV when the corresponding PVC is deleted. Possible values are `Retain`, `Recycle`, and `Delete`. In this example, it's set to `Retain` which means the PV will be left as-is even after the PVC is deleted, and an administrator needs to take action manually.
- `spec.storageClassName`: The Storage Class name to be used for dynamically provisioning this volume. If not specified, it becomes a statically provisioned volume.
- `spec.hostPath.path`: The local directory path on the node's filesystem where the volume data will be stored. This field is specific to the hostPath volume type and should not be used in a production environment.


## Commands

### General
`kubectl` provides several commands to work with Persistent Volumes (PVs) in Kubernetes. Here are some of the most commonly used `kubectl` commands related to Persistent Volumes:

```bash
kubectl get persistentvolumes
kubectl describe persistentvolume example-pv
kubectl delete persistentvolume example-pv
kubectl edit persistentvolume example-pv
kubectl apply -f pv.yaml
```

### Imperative
Now, let's see an example of how to create the same PersistentVolume using the imperative command:

```bash
kubectl create pv example-pv --capacity=5Gi --access-modes=ReadWriteOnce --persistent-volume-reclaim-policy=Retain --storage-class=slow --host-path=/data/persistent_volume
```

Different types of `accessModes`:
- `ReadWriteOnce`: The volume can be mounted as read-write by a single node.
- `ReadOnlyMany`: The volume can be mounted read-only by many nodes.
- `ReadWriteMany`: The volume can be mounted as read-write by many nodes.

These access modes define how the volume can be mounted by pods, and the choice depends on the specific requirements of your application. Keep in mind that not all storage types support all access modes, so you need to choose an appropriate storage class that provides the desired access mode based on your application's needs.