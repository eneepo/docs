---
title: "Replication Controller"
description: "A Replication Controller (often abbreviated as RC) is an older, now-deprecated resource used for ensuring a specified number of replicas of a pod are running in a cluster."
date: 2023-07-09T16:58:23+02:00
lastmod: 2023-07-09T16:58:23+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resources"
weight: 1010
toc: true
---
A Replication Controller (often abbreviated as RC) is an older, now-deprecated resource used for ensuring a specified number of replicas of a pod are running in a cluster. It is a lower-level concept compared to ReplicaSets, and it has largely been replaced by [ReplicaSets]({{< ref "/kubernetes/resources/replica-set.md" >}} "ReplicaSets")  and [Deployments]({{< ref "/kubernetes/resources/deployment.md" >}} "Deployments") for managing pod replication and scaling.

The Replication Controller works similarly to the ReplicaSet in that it maintains a desired number of pod replicas, but it lacks some of the advanced features provided by ReplicaSets and Deployments.

```yaml
#rcd-definition.yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
  replicas: 3
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
k create -f rc-definition.yaml
k apply -f rc-definition.yaml
k get replicationcontroller
```