---
title: "Taint, Toleration"
description: ""
date: 2023-08-27T07:34:43Z
lastmod: 2023-08-27T07:34:43Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resource-management"
weight: 1225
toc: true
---

Taints and tolerations help control how pods are scheduled onto nodes in a cluster. They are essential for ensuring that certain pods are placed on specific nodes or avoided on others based on specific requirements or constraints.

## Taint

A taint is a property assigned to a node in a Kubernetes cluster. It is essentially a constraint that specifies that a node should not accept certain pods unless those pods have corresponding tolerations.

### Use Case

Taints are typically used in scenarios where you want to reserve nodes for specific workloads, hardware, or other requirements. For instance, you might have nodes with GPUs and want to ensure that only pods requiring GPU resources are scheduled on those nodes.

### Syntax

Taints have a simple syntax consisting of three parts:

- **Key:** A descriptive string, often representing the reason for the taint.
- **Value:** An arbitrary string used to further categorize the taint.
- **Effect:** This can be one of three values:
    - `NoSchedule`: Pods that do not tolerate the taint will not be scheduled on the tainted node.
    - `PreferNoSchedule`: Kubernetes will try to avoid scheduling pods that do not tolerate the taint on the node, but it's not an absolute prohibition.
    - `NoExecute`: Existing pods on a node that do not tolerate this taint will be evicted if the taint is added to the node.

### Commands

You add a taint to a node using kubectl taint. For example,

```bash
# Add a taint to node-1
kubectl taint nodes node-1 app=critical:NoSchedule

# To remove the taint added by the command above, you can run:
kubectl taint nodes node-1 app=critical:NoSchedule-

# View the tains for the kubemaster node
kubectl describe node kubemaster | grep Taint
```

## Toleration

A toleration is a pod-level configuration that allows a pod to be scheduled onto nodes with specific taints. In other words, tolerations specify that a pod is willing to tolerate certain node conditions that would otherwise prevent it from being scheduled on that node.

### Use Case

Tolerations are used to ensure that pods that need to run on nodes with particular taints can do so. They are especially useful when you have specific node requirements or constraints that should not affect all pods in the cluster.

### Syntax

Tolerations are defined in a pod's YAML specification. Each toleration has the following components:

- **Key:** The same key as the taint.
- **Value:** The same value as the taint.
- **Effect:** Should match the effect of the taint (`NoSchedule`, `PreferNoSchedule`, or `NoExecute`).
- **Operator:** Can be `Equal` or `Exists`. `Equal` means the key and value must match exactly, while `Exists` means only the key needs to exist.

### Example

Here's an example of a toleration in a pod's YAML configuration that allows it to run on nodes with the "app=critical" taint:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: my-pod
  name: my-pod
spec:
  containers:
    - image: nginx
      name: my-pod
      resources: {}
  tolerations:
    - key: app
      operator: Equal
      value: critical
      effect: NoSchedule
```