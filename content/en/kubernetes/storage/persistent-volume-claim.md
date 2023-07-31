---
title: "Persistent Volume Claim"
description: ""
date: 2023-07-30T21:17:14Z
lastmod: 2023-07-30T21:17:14Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "storage"
weight: 1015
toc: true
---
A Persistent Volume Claim (PVC) is a Kubernetes resource that allows a pod to request a specific amount of storage from a Persistent Volume (PV). PVCs provide a way to abstract the underlying storage implementation from the pod, making it easier to manage and migrate data when needed.

## Roles and Responsibilities:
  - A Persistent Volume Claim is a request for storage by a pod. It acts as a request for a specific amount of storage with specific characteristics.
  - PVCs are used by pods to claim a suitable PV based on their storage requirements (e.g., capacity, access mode).
  - PVCs are namespaced resources, meaning they are created within a specific namespace and only exist within that namespace.
  - When a pod requests storage via a PVC, Kubernetes looks for an available PV that matches the PVC's requirements (capacity, access modes, storage class, etc.).
  - If a suitable PV is found, it is bound to the PVC, and the pod can use the storage defined in the PV.
  - If no suitable PV is available, some storage classes can dynamically provision a PV that matches the PVC's requirements.

## spec
### accessModes
The `accessModes` field in a Persistent Volume (PV) specifies the access modes that are allowed for the storage volume. It defines how the volume can be mounted by pods that use the corresponding Persistent Volume Claim (PVC).

The `accessModes` field can have one or more of the following access mode values:

1. `ReadWriteOnce` (RWO): This access mode allows the volume to be mounted as read-write by a single node. It means that the volume can be attached to and used by only one node in the cluster at a time. This mode is suitable for scenarios where data needs to be shared between pods running on the same node.

2. `ReadOnlyMany` (ROX): This access mode allows the volume to be mounted as read-only by multiple nodes. It means that multiple pods running on different nodes in the cluster can read data from the volume, but none of them can write to it. This mode is suitable for scenarios where multiple pods need to access the same data in a read-only manner.

3. `ReadWriteMany` (RWX): This access mode allows the volume to be mounted as read-write by multiple nodes. It means that multiple pods running on different nodes in the cluster can both read from and write to the volume simultaneously. However, not all storage types support this mode, so it's essential to verify that your storage class and underlying storage system support it before using this access mode.

### persistentVolumeReclaimPolicy
The `persistentVolumeReclaimPolicy` is a property of Persistent Volumes (PVs) in Kubernetes. It specifies what should happen to the PV's data when the corresponding Persistent Volume Claim (PVC) is deleted or released.

There are three possible options for the `persistentVolumeReclaimPolicy`:

1. `Retain`: When a PVC is deleted or released, the associated PV's data will be retained. The PV will not be deleted, and the data will remain intact. This allows administrators to manually recover the data if needed. For critical data that needs to be preserved even after the PVC is deleted, you should use the `Retain` policy. 

2. `Delete`: When a PVC is deleted or released, the associated PV's data will be deleted as well. The PV will be removed from the cluster, and the underlying storage resources will be released. On the other hand, for non-critical or temporary data, the `Delete` policy might be more appropriate, as it allows the storage resources to be reclaimed when no longer needed.

3. `Recycle`: This option has been deprecated and is no longer recommended for use. It was previously used for dynamic provisioning of volumes, where the PV's data was deleted, and the PV was made available for reuse by other PVCs.

The default value for `persistentVolumeReclaimPolicy` is typically set by the storage system or the dynamic provisioner, depending on the storage class used for the Persistent Volume.

Keep in mind that the `persistentVolumeReclaimPolicy` only applies to dynamically provisioned PVs or statically provisioned PVs that were requested by a PVC. For PVs not associated with a PVC, their reclaim policy is typically determined by the administrator's manual intervention.

## Example
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
      storage: 3Gi
  # The Storage Class name to be used for dynamically provisioning this claim. If not specified, it becomes a statically provisioned claim.
  storageClassName: slow
```
In the following example, the pod is using the `example-pvc` claim through the `data-volume` volume. The data stored in `/data` inside the pod will be persistent, and if the pod is rescheduled or deleted, it can reclaim the same data from the bound PV associated with the PVC.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  volumes:
    - name: data-volume
      persistentVolumeClaim:
        claimName: example-pvc
  containers:
    - name: app-container
      image: your_app_image
      volumeMounts:
        - name: data-volume
          mountPath: /data
```

## Commands
### General
Remember to replace `<claim-name>`, `<access-modes>`, `<storage-class>`, and `<storage-size>` with the appropriate values for your PVC. The commands in this cheatsheet will help you manage and interact with Persistent Volume Claims in your Kubernetes cluster.

```bash
kubectl create persistentvolumeclaim <claim-name> --access-modes=<access-modes> --storage-class=<storage-class> --request-storage=<storage-size>
kubectl get persistentvolumeclaims
kubectl describe persistentvolumeclaim <claim-name>
kubectl edit persistentvolumeclaim <claim-name>
kubectl delete persistentvolumeclaim <claim-name>
kubectl apply -f <pvc_yaml_file>
kubectl describe persistentvolumeclaim <claim-name> | grep "Volume:"  # Describe the Persistent Volume associated with a PVC
kubectl describe persistentvolumeclaim <claim-name> | grep "Bound"    # Check the status of the Persistent Volume Binding
```

### Imperative
You can create a Persistent Volume Claim (PVC) using the imperative `kubectl` command. Here's the imperative command to create a PVC:

```bash
kubectl create persistentvolumeclaim <claim-name> --access-modes=<access-modes> --storage-class=<storage-class> --request-storage=<storage-size>
```