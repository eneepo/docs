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
weight: 1010
toc: true
---
A Persistent Volume Claim (PVC) is a Kubernetes resource that allows a pod to request a specific amount of storage from a Persistent Volume (PV). PVCs provide a way to abstract the underlying storage implementation from the pod, making it easier to manage and migrate data when needed.

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
  storageClassName: slow
```

Explanation of the fields in the YAML file:
- `metadata.name`: The name of the Persistent Volume Claim.
- `spec.accessModes`: The access modes the claim requests. Multiple modes can be specified (comma-separated), but not all combinations are supported. Common modes include:
  - `ReadWriteOnce`: The claim can be mounted as read-write by a single node.
  - `ReadOnlyMany`: The claim can be mounted read-only by many nodes.
  - `ReadWriteMany`: The claim can be mounted as read-write by many nodes (not supported by all volume types).
- `spec.resources.requests.storage`: The amount of storage requested for the claim. In this example, the claim requests 3 gigabytes of storage.
- `spec.storageClassName`: The Storage Class name to be used for dynamically provisioning this claim. If not specified, it becomes a statically provisioned claim.

With this Persistent Volume Claim, the pod can use the storage requested in the claim. If the cluster has a Persistent Volume that meets the criteria (requested storage, access modes, etc.) defined in the PVC, Kubernetes will bind the claim to that specific PV. If there is no suitable PV available, Kubernetes may dynamically provision one based on the Storage Class defined in the PVC.

To use this PVC in a pod, you would reference it in the pod's YAML file under the `volumes` section. For example:

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

In this example, the pod is using the `example-pvc` claim through the `data-volume` volume. The data stored in `/data` inside the pod will be persistent, and if the pod is rescheduled or deleted, it can reclaim the same data from the bound PV associated with the PVC.

## Commands
### General
Sure! Here's a cheatsheet of `kubectl` commands related to Persistent Volume Claims (PVCs):

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
Remember to replace `<claim-name>`, `<access-modes>`, `<storage-class>`, and `<storage-size>` with the appropriate values for your PVC. The commands in this cheatsheet will help you manage and interact with Persistent Volume Claims in your Kubernetes cluster.

### Imperative
You can create a Persistent Volume Claim (PVC) using the imperative `kubectl` command. Here's the imperative command to create a PVC:

```bash
kubectl create persistentvolumeclaim <claim-name> --access-modes=<access-modes> --storage-class=<storage-class> --request-storage=<storage-size>
```

Let's break down the command and its parameters:

- `<claim-name>`: This is the name you want to give to the Persistent Volume Claim. Choose a unique and descriptive name for your claim.

- `--access-modes`: This specifies the access modes the claim requests. You can use one or more of the following access modes (separated by commas):
  - `ReadWriteOnce`: The claim can be mounted as read-write by a single node.
  - `ReadOnlyMany`: The claim can be mounted read-only by many nodes.
  - `ReadWriteMany`: The claim can be mounted as read-write by many nodes (not supported by all volume types).

- `--storage-class`: This specifies the name of the Storage Class to be used for dynamically provisioning the claim. If you want to use a specific Storage Class, provide its name here. If you omit this parameter, the claim will be bound to a statically provisioned Persistent Volume.

- `--request-storage`: This specifies the amount of storage to request for the claim. Use a valid size unit (e.g., Gi for gigabytes, Mi for megabytes). For example, to request 5 gigabytes, use `5Gi`.

Here's an example of how to create a Persistent Volume Claim using the imperative command:

```bash
kubectl create persistentvolumeclaim example-pvc --access-modes=ReadWriteOnce --storage-class=slow --request-storage=3Gi
```

In this example, we are creating a PVC named `example-pvc` with the access mode set to `ReadWriteOnce`, requesting 3 gigabytes of storage, and using the `slow` Storage Class for dynamic provisioning.

Keep in mind that using imperative commands is convenient for quick tasks, but it's generally recommended to define your resources in YAML files (declarative approach) to ensure better version control, traceability, and reproducibility of your Kubernetes configurations.