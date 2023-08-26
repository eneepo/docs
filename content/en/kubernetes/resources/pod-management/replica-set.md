---
title: "Replica Set"
description: "A ReplicaSet is a high-level abstraction that ensures a specified number of identical pod replicas are running at all times. It is one of the key resources used for managing and scaling applications in a Kubernetes cluster."
date: 2023-07-09T16:58:56+02:00
lastmod: 2023-07-09T16:58:56+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "pod-management"
weight: 1010
toc: true
---
A ReplicaSet is a high-level abstraction that ensures a specified number of identical pod replicas are running at all times. ReplicaSets were introduced as a more powerful and flexible replacement for Replication Controller.

The main purpose of a ReplicaSet is to provide horizontal pod scaling, ensuring that a specific number of replicas of a pod are always running. If a pod or node fails or is terminated, the ReplicaSet will automatically create new replicas to replace the ones that were lost. Likewise, if the desired number of replicas is increased, the ReplicaSet will create additional pods to match the desired count.

```yaml
# rs-definition.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-rs                         # The name of the ReplicaSet resource.
spec:
  replicas: 3                         # The desired number of Pod replicas.
  selector:
    matchLabels:
      app: my-app                     # The label selector to match Pods managed by this ReplicaSet.
  template:
    metadata:
      labels:
        app: my-app                   # The labels to apply to Pods created by this ReplicaSet.
    spec:
      containers:
        - name: my-nginx-container
          image: nginx                # The Docker image to use for the Pods.

```

## Commands
```shell
# Explain the ReplicaSet resource, displaying its documentation.
kubectl explain rs

# Create a new ReplicaSet based on the configuration defined in 'replicaset-definition.yaml'.
kubectl create -f replicaset-definition.yaml

# Apply the configuration in 'replicaset-definition.yaml' to create or update the ReplicaSet.
kubectl apply -f replicaset-definition.yaml

# Replace the existing ReplicaSet with the one defined in 'replicaset-definition.yaml'.
kubectl replace -f replicaset-definition.yaml

# Scale the ReplicaSet defined in 'replicaset-definition.yaml' to have 6 replicas.
kubectl scale --replicas=6 -f replicaset-definition.yaml

# Scale the named ReplicaSet 'myapp-replicaset' to have 6 replicas.
kubectl scale --replicas=6 replicaset myapp-replicaset

# List all ReplicaSets in the current Kubernetes cluster and namespace.
kubectl get replicaset

# Describe the details of the ReplicaSet 'myapp-rs', including its current status.
kubectl describe replicaset myapp-rs

# Force creating new pods
kubectl delete pods $(k get pods --no-headers -o custom-columns=":metadata.name")
```