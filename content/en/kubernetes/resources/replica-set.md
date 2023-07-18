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
    parent: "resources"
weight: 1015
toc: false
---
A ReplicaSet is a high-level abstraction that ensures a specified number of identical pod replicas are running at all times. It is one of the key resources used for managing and scaling applications in a Kubernetes cluster. ReplicaSets are part of the Kubernetes controller system and provide the ability to maintain the desired state of a set of pods.

The main purpose of a ReplicaSet is to provide horizontal pod scaling, ensuring that a specific number of replicas of a pod are always running. If a pod or node fails or is terminated, the ReplicaSet will automatically create new replicas to replace the ones that were lost. Likewise, if the desired number of replicas is increased, the ReplicaSet will create additional pods to match the desired count.

```yaml
#replicaset-definition.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
  replicas: 3
  selector:
    matchLabels:
      type: front-end
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
```

Commands
```shell
k explain rs
k create -f replicaset-definition.yaml
k apply -f replicaset-definition.yaml
k replace -f replicaset-definition.yaml
k scale --replicas=6 -f replicaset-definition.yaml
k scale --replicas=6 replicaset myapp-replicaset
k get replicaset
k describe replicaset myapp-rs
```