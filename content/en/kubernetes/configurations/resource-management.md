---
title: "Resource Quota & Management"
description: ""
date: 2023-07-26T07:46:52+02:00
lastmod: 2023-07-26T07:46:52+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1090
toc: true
---

## Namespace Resource Quota
Limiting a namespace resource quota
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```
## Resource Requests vs Limits
### Resource Requests
Resource requests represent the amount of resources that a container initially requires to run. They serve as a means of informing the Kubernetes scheduler about the container's resource requirements. When a container specifies its resource requests, Kubernetes uses this information to make intelligent scheduling decisions, placing the container on a node with sufficient resources to meet its needs.

Example: Consider a container that runs a CPU-intensive task. By specifying a CPU request of 500 milliCPU (0.5), you are indicating to Kubernetes that the container requires at least half of a CPU core to run optimally. The scheduler will attempt to find a node that can accommodate this request and schedule the container accordingly.

### Resource Limits
Resource limits specify the maximum amount of resources that a container can consume. They are used to prevent a container from using more resources than allocated, which helps maintain stability and fairness within the cluster. Setting resource limits is important to prevent a single container from monopolizing resources and impacting the performance of other containers.

Example: Let's consider a container that runs a memory-intensive application. By setting a memory limit of 1 gigabyte (1Gi), you are restricting the container from using more than the allocated memory. If the application tries to consume more memory, Kubernetes will take action, such as terminating the container or throttling its resource usage.

## Units
### CPU resource units
Limits and requests for CPU resources are measured in cpu units. In Kubernetes, 1 CPU unit is equivalent to 1 physical CPU core, or 1 virtual core, depending on whether the node is a physical host or a virtual machine running inside a physical machine.

Fractional requests are allowed. When you define a container with `spec.containers[].resources.requests.cpu` set to 0.5, you are requesting half as much CPU time compared to if you asked for 1.0 CPU. For CPU resource units, the quantity expression 0.1 is equivalent to the expression 100m, which can be read as "one hundred millicpu". Some people say "one hundred millicores", and this is understood to mean the same thing.

### Memory resource units
Limits and requests for memory are measured in bytes. You can express memory as a plain integer or as a fixed-point number using one of these quantity suffixes: E, P, T, G, M, k. You can also use the power-of-two equivalents: Ei, Pi, Ti, Gi, Mi, Ki. For example, the following represent roughly the same value:

```
128974848, 129e6, 129M,  128974848000m, 123Mi
```

## Examples
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: my-image
      resources:
        requests:
          cpu: "500m"
          memory: "1Gi"
        limits:
          cpu: "1"
          memory: "12Gi"
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: elephant
spec:
  containers:
  - name: mem-stress
    image: polinux/stress
    imagePullPolicy: Always
    command:
    - stress
    args:
    - --vm
    - "1"
    - --vm-bytes
    - 15M
    - --vm-hang
    - "1"
    resources:
      limits:
        memory: 20Mi
      requests:
        memory: 5Mi
```