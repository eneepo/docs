---
title: "Services"
description: ""
date: 2023-07-29T15:51:53Z
lastmod: 2023-07-29T15:51:53Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "services"
weight: 1000
toc: true
---

There are different types of services that allow you to expose applications running inside a cluster to the external world or to other services within the Kubernetes cluster. Here are the commonly used service types along with example YAML definition files for each:

## ClusterIP (default)
This is the default service type. It exposes the service on a cluster-internal IP address. The service is only reachable from within the cluster. It is ideal for inter-pod communication.

## NodePort
This type of service exposes the service on each node's IP at a static port. It allows access to the service from outside the cluster by using the node's IP address and the NodePort. Although this service type is easy to set up, it's not recommended for production deployments as it exposes the service on a potentially wide range of ports.