---
title: "Node Affinity"
description: ""
date: 2023-08-27T14:53:54Z
lastmod: 2023-08-27T14:53:54Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resource-management"
weight: 1227
toc: true
---

Node affinity allows you to influence the scheduling of pods based on rules defined in the pod's specification. It enables you to control which nodes in your cluster should or should not run specific pods, which can be crucial for various reasons, such as optimizing resource utilization, ensuring data locality, or compliance requirements.

Node affinity can be categorized into two types: 

1. **Node Affinity**: This type of affinity allows you to specify rules that positively affect pod placement, ensuring that pods are scheduled on nodes that match certain criteria.

2. **Node Anti-Affinity**: Conversely, node anti-affinity allows you to specify rules that negatively affect pod placement, ensuring that pods are not scheduled on nodes that match certain criteria.

Here are some examples to illustrate the use of node affinity:

## Node Affinity Example:

Suppose you have a Kubernetes cluster running both GPU and CPU-intensive workloads. You want to ensure that your GPU-intensive pods are scheduled only on nodes with GPUs and your CPU-intensive pods on nodes without GPUs.

```yaml
apiVersion: v1                                       # Specifies the Kubernetes API version to use for this resource, in this case, 'v1'.
kind: Pod                                            # Indicates that we are creating a Pod resource.

metadata:
  name: gpu-pod                                       # Specifies the name of the Pod, which will be used to identify it within the cluster.
  
spec:                                                # The 'spec' section defines the specifications for the Pod.
  affinity:                                           # Affinity rules allow you to influence the scheduling of this Pod on specific nodes.

    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution: # indicates that this rule must be satisfied during scheduling but can be ignored afterward.
        nodeSelectorTerms:                            # is an array that allows you to specify multiple node selection criteria.
          
        - matchExpressions:                           # Defines the matching criteria for node selection.
            - key: gpu                                # 'key' specifies the node label key that we are matching against.
              operator: In                            # 'operator' specifies the type of comparison to perform, in this case, 'In' checks if the label value is within the provided list of values.
              values:
              - "true"                                # 'values' is a list of values to compare the label key against. This Pod will be scheduled on nodes where the 'gpu' label is set to "true".

  containers:                                         # 'containers' section defines the containers that should run within the Pod.
  - name: gpu-container
    image: gpu-app-image                              # 'image' specifies the Docker image to use for this container.

```

In this example, the `nodeAffinity` section specifies that this pod requires a node with a label `gpu=true`. Kubernetes will schedule this pod only on nodes that have the label `gpu=true`.

## Node Anti-Affinity Example:

Let's say you have a stateful application deployed in your cluster and you want to ensure that no two instances of this application run on the same node for high availability.

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: stateful-app  # Name of the StatefulSet resource
spec:
  serviceName: "stateful-app"  # Name of the headless service associated with the StatefulSet
  replicas: 3  # Number of desired replicas for the StatefulSet
  selector:
    matchLabels:
      app: stateful-app  # Label selector to match pods controlled by this StatefulSet
  template:
    metadata:
      labels:
        app: stateful-app  # Labels applied to pods created from this template
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:  # Anti-affinity rules to prevent co-location of pods
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - stateful-app  # Label values for anti-affinity matching
            topologyKey: "kubernetes.io/hostname"  # Node topology used for anti-affinity decisions
      containers:
      - name: stateful-container  # Name of the container within the pod
        image: stateful-app-image  # Docker image used for the container
```

In this example, the `podAntiAffinity` section specifies that pods with the label `app: stateful-app` should not be scheduled on nodes where other pods with the same label are already running. This ensures that instances of your stateful application are distributed across different nodes for better fault tolerance.

Node affinity and anti-affinity rules can be more complex, using multiple match expressions, topology keys, and other criteria to fine-tune pod scheduling according to your specific requirements. These rules provide greater control over how pods are distributed across nodes in your Kubernetes cluster, helping you achieve better resource utilization, performance, and high availability.