---
title: "Resouce Quota"
description: ""
date: 2023-07-18T08:11:02+02:00
lastmod: 2023-07-18T08:11:02+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resources"
weight: 1030
toc: false
---
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