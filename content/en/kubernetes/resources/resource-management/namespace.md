---
title: "Namespace"
description: ""
date: 2023-08-26T16:02:41Z
lastmod: 2023-08-26T16:02:41Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resource-management"
weight: 1205
toc: true
---

In Kubernetes, a namespace is a logical and virtual cluster within a physical Kubernetes cluster. Namespaces are used to create isolated spaces where resources like pods, services, and volumes can be organized and managed. They provide a way to divide a Kubernetes cluster into multiple virtual clusters, each with its own set of resources and policies. This helps in organizing and isolating workloads, particularly in multi-tenant or complex environments.

```yaml
# kubectl create namespace my-namespace -o yaml --dry-run=client

apiVersion: v1                  # Specifies the API version of the Kubernetes object being created.
kind: Namespace                 # Indicates that we are creating a Namespace resource.

metadata:                       # Metadata section contains information about the Namespace resource.
  creationTimestamp: null         # The timestamp at which this Namespace was created (null means it hasn't been created yet).
  name: my-namespace              # Specifies the name of the Namespace. In this case, it's named "my-namespace".

spec:
  # The 'spec' section can be used to specify additional configuration settings for the Namespace.
  # In this case, it's left empty, meaning no specific configuration settings are defined.

status:
  # The 'status' section provides information about the current status of the Namespace.
  # Since it's left empty in this YAML file, it means there is no specific status to report.
```

## Key points

1. **Isolation**: Namespaces provide a level of resource isolation. Resources created in one namespace are typically not visible or accessible to other namespaces, providing a form of separation between different applications or teams.

2. **Scope**: Namespaces have a scope limited to the cluster they are created in. This means that resources with the same name can exist in different namespaces without conflict.

3. **Default Namespace**: When you create resources without specifying a namespace, they are typically placed in the default namespace, which is automatically created when you set up a Kubernetes cluster. This namespace is intended for resources that don't belong to any specific namespace.

4. **Not all objects are in a namespace**: Most Kubernetes resources (e.g. pods, services, replication controllers, and others) are in some namespaces. However namespace resources are not themselves in a namespace. And low-level resources, such as nodes and persistentVolumes, are not in any namespace. To see which Kubernetes resources are and aren't in a namespace:

    ```shell
    # In a namespace
    kubectl api-resources --namespaced=true

    # Not in a namespace
    kubectl api-resources --namespaced=false
    ```

## Common Namespaces  

By default, when you set up a Kubernetes cluster, it typically includes the following namespaces:

### default

As mentioned, this is the namespace where resources are placed if no other namespace is specified. It's the catch-all namespace for resources that do not need to be isolated.

### kube-system

This namespace contains essential Kubernetes system resources, such as the control plane components (like the API server, controller manager, and scheduler), as well as other system-level resources like DNS.

### kube-public

This namespace is often used to store configuration or information that should be made available to all users or applications within the cluster. It's usually read-only for most users.

### kube-node-lease

Introduced in Kubernetes 1.14 and later, this namespace is used for node lease objects that nodes in the cluster use to communicate their availability.

### kube-ingress

This namespace may be present if you have an Ingress Controller installed. It's used to manage ingress-related resources.

### kube-admission

This namespace is used for admission controllers and may be present in some cluster configurations.

It's important to note that these are the namespaces commonly found in a standard Kubernetes cluster. However, the namespaces available by default can vary depending on how your cluster is set up, and you can create custom namespaces as needed to organize your workloads and resources effectively.

## Service Discovery
Service Discovery via DNS with Namespace

### Inside of the Namespace

Use the following format to access a service within the namespace:

```python
mysql.connect('db-service')
```

### Outside of the Namespace

Use the following format to access a service in another namespace: `<service-name>.<namespace>.svc.cluster.local`

```python
mysql.connect('db-service.my-namespace.svc.cluster.local')
```

To find the cluster domain name use:
```bash
kubectl describe cm coredns -n kube-system
```
or
```bash
kubectl get cm coredns -n kube-system -o jsonpath="{.data.Corefile}"
```

## Commands

```shell
# List all namespaces in the Kubernetes cluster.
kubectl get namespace

# Abbreviated form of 'kubectl get namespace', listing all namespaces.
kubectl get ns

# List all pods across all namespaces in the Kubernetes cluster.
kubectl get pods --all-namespaces

# Abbreviated form of 'kubectl get pods --all-namespaces', listing all pods.
kubectl get pods -A

# Create a new pod named 'nginx' using the 'nginx' Docker image in a specific namespace.
kubectl run nginx --image=nginx --namespace=<name>

# Create a new pod named 'nginx' using the 'nginx' Docker image in a specific namespace (alternative syntax).
kubectl run nginx --image=nginx -n=<name>

# List all pods in a specific namespace named '<name>'.
kubectl get pods --namespace=<name>

# List all pods in a specific namespace named '<name>' (alternative syntax).
kubectl get pods --n=<name>

# Set the current context's namespace to 'dev'.
kubectl config set-context $(kubectl config current-context) --namespace=dev

# Set the current context's namespace to '<name>'.
kubectl config set-context --current --namespace=<name>

# View the Kubernetes configuration, and filter it to show the 'namespace' field.
kubectl config view --minify | grep namespace
```