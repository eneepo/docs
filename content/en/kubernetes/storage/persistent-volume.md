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
weight: 1010
toc: true
---

In Kubernetes, a `PersistentVolume` (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. It is a way to decouple storage provision from the lifecycle of pods that use the storage. PVs are used to store data in a durable and independent way, and they exist outside the lifecycle of pods. Pods can claim a PV using a `PersistentVolumeClaim` (PVC), and Kubernetes ensures that the same PV is mounted to the pod if the PVC requests it.

## Roles and Responsibilities:
  - A Persistent Volume represents a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using a storage class.
  - PVs are provisioned independently of any specific pod and exist as a cluster-level resource.
  - PVs have a lifecycle separate from pods, meaning they persist data even if the pods using them are deleted or rescheduled.
  - Administrators or automation tools are responsible for managing PVs, which includes provisioning, resizing, and reclaiming the storage.
  - PVs have various properties such as capacity, access modes, storage class, reclaim policy, etc., which define how they can be used and reclaimed.
  - Pods can claim a PV by creating a Persistent Volume Claim (PVC) that matches the PV's specifications.

In Kubernetes, a Persistent Volume (PV) resource is used to represent a piece of storage in the cluster that is provisioned independently of any specific pod. The PV provides a way to decouple storage provision from the lifecycle of pods using that storage. Let's explore the fields present in the PV resource:

## spec
### accessModes
  - `accessModes` is a required field that specifies the access modes the volume allows. It defines how the volume can be mounted by pods that use the corresponding Persistent Volume Claim (PVC).
  - It can have one or more of the following values:
    - `ReadWriteOnce` (RWO): The volume can be mounted as read-write by a single node.
    - `ReadOnlyMany` (ROX): The volume can be mounted read-only by many nodes.
    - `ReadWriteMany` (RWX): The volume can be mounted as read-write by many nodes (not supported by all volume types).
  - The choice of access modes depends on your application's requirements and the capabilities of the underlying storage system.

### persistentVolumeReclaimPolicy
  - `persistentVolumeReclaimPolicy` is an optional field that specifies what should happen to the PV's data when the corresponding Persistent Volume Claim (PVC) is deleted or released.
  - It can have one of the following values:
    - `Retain`: When a PVC is deleted or released, the associated PV's data will be retained. The PV will not be deleted, and the data will remain intact, allowing administrators to manually recover the data if needed.
    - `Delete`: When a PVC is deleted or released, the associated PV's data will be deleted as well. The PV will be removed from the cluster, and the underlying storage resources will be released.
    - `Recycle`: This value has been deprecated and is no longer recommended for use. It was previously used for dynamic provisioning of volumes, where the PV's data was deleted, and the PV was made available for reuse by other PVCs.

### storageClassName
  - `storageClassName` is an optional field that specifies the name of the Storage Class associated with the PV.
  - The Storage Class defines the provisioning parameters and characteristics of the PV, such as the type of storage, access modes, and other storage-related parameters.
  - If a PV has a `storageClassName` defined, it means it was provisioned dynamically based on the Storage Class. If it's empty, the PV is statically provisioned.

### hostPath
  - It is a local directory path on the node's filesystem where the volume data will be stored.
  - This is a specific type of PV that might not be suitable for production use since it relies on the node's local storage, which is not shared across the cluster and can lead to data loss if the node fails.

## Example
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

## Commands

### General
Here are some of the most commonly used `kubectl` commands related to Persistent Volumes:

```bash
kubectl get persistentvolumes
kubectl describe persistentvolume example-pv
kubectl delete persistentvolume example-pv
kubectl edit persistentvolume example-pv
kubectl apply -f pv.yaml
```

### Imperative
Example of how to create the same PersistentVolume using the imperative command:

```bash
kubectl create pv example-pv --capacity=5Gi --access-modes=ReadWriteOnce --persistent-volume-reclaim-policy=Retain --storage-class=slow --host-path=/data/persistent_volume
```