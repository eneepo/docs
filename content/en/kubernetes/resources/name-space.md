---
title: "Name Space"
description: ""
date: 2023-07-18T08:08:25+02:00
lastmod: 2023-07-18T08:08:25+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resources"
weight: 1025
toc: true
---
Kubernetes starts with four initial namespaces:

* default:
Kubernetes includes this namespace so that you can start using your new cluster 
without first creating a namespace.

* kube-node-lease:
This namespace holds Lease objects associated with each node. Node leases allow the kubelet to send heartbeats so that the control plane can detect node failure.

* kube-public:
This namespace is readable by all clients (including those not authenticated). This namespace is mostly reserved for cluster usage, in case that some resources should be visible and readable publicly throughout the whole cluster. The public aspect of this namespace is only a convention, not a requirement.

* kube-system:
The namespace for objects created by the Kubernetes system

## make sure your pod is created in a certain namespace

```yaml
# namespace-definition.yaml
apiVersion: v1
kind: NameSpace
metadata:
  name: dev
```

```yaml
# pod-definition.yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  namespace: dev
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

## Commands
```shell
# Viewing namespaces 
k get namespace
k get ns
k get pods --all-namespaces
k get pods -A

# Setting the namespace for a request
kubectl run nginx --image=nginx --namespace=<name>
kubectl run nginx --image=nginx -n=<name>
kubectl get pods --namespace=<name>
kubectl get pods --n=<name>


# Setting the namespace preference 
k config set-context $(k config current-context) --namespace=dev
k config set-context --current --namespace=<name>
# Validate it
k config view --minify | grep namespace:
```

## Namespaces and DNS 
When you create a Service, it creates a corresponding DNS entry. This entry is of the form `<service-name>.<namespace-name>.svc.cluster.local`, which means that if a container only uses `<service-name>`, it will resolve to the service which is local to a namespace. This is useful for using the same configuration across multiple namespaces such as Development, Staging and Production. If you want to reach across namespaces, you need to use the fully qualified domain name (FQDN).

```python
mysql.connect('db-service')
mysql.connect('db-service.dev.svc.cluster.local')
```

## Not all objects are in a namespace 
Most Kubernetes resources (e.g. pods, services, replication controllers, and others) are in some namespaces. However namespace resources are not themselves in a namespace. And low-level resources, such as nodes and persistentVolumes, are not in any namespace.

To see which Kubernetes resources are and aren't in a namespace:

```shell
# In a namespace
kubectl api-resources --namespaced=true

# Not in a namespace
kubectl api-resources --namespaced=false
```