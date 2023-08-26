---
title: "Node Port"
description: ""
date: 2023-07-29T16:03:17Z
lastmod: 2023-07-29T16:03:17Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "networking"
weight: 1105
toc: true
---
In Kubernetes, a NodePort service is a type of service that exposes an application running inside a cluster to be accessible from outside the cluster. It does this by allocating a specific port on every node in the cluster, and any traffic that comes to that port on any node is forwarded to the service. NodePort services are typically used when you need to make your application accessible from outside the Kubernetes cluster, for example, for testing or to allow external clients to access your application.

Here are the key components related to a NodePort service in a Kubernetes YAML specification:

## Ports
The port field in the specs is an array that specifies the ports on which the service should listen. It contains the following subfields:

- **nodePort:** This is the port number on which the service will be accessible on each node in the cluster. This field is optional; if not specified, Kubernetes will allocate a random port in the range 30000-32767.
- **port:** This is the port number on which the service listens within the cluster.
- **targetPort:** This is the port number on which the pods (containers) in the associated deployment or replica set are listening. Traffic received on the service's port is forwarded to this targetPort.

![NodePort](/images/k8s/node-port.png "NodePort Kubernetes Service")

## Example

```yaml
# kubectl create service nodeport my-nodeport-service --tcp=80:8080 --node-port=30080 --dry-run=client -o yaml > my-nodeport-service.yaml

apiVersion: v1              # Specifies the Kubernetes API version being used for this resource.
kind: Service               # Specifies the type of Kubernetes resource, in this case, a Service.

metadata:
  creationTimestamp: null     # The timestamp when this Service was created, null since it hasn't been created yet.
  labels:
    app: my-nodeport-service  # Labels used to identify and group related resources.
  name: my-nodeport-service   # The name of the Service.

spec:
  ports:
  - name: 80-8080              # A human-readable name for the port configuration.
    nodePort: 30080            # The port on the host (Node) that the Service should listen on.
    port: 80                   # The port on which the Service will listen internally.
    protocol: TCP              # The protocol used for this port (TCP in this case).
    targetPort: 8080           # The port on the target Pods to which traffic should be forwarded.

  selector:
    app: my-app                # Selects the Pods that this Service should route traffic to based on their labels.

  type: NodePort               # Specifies that this Service should be of type NodePort, exposing it on a high port on all Nodes.

status:
  loadBalancer: {}             # The status of the Service's load balancer, which is empty in this case.
```

In summary, this YAML file defines a Kubernetes Service named `my-nodeport-service` of type NodePort, which listens on port 80 on all Nodes and forwards traffic to Pods labeled with `app: my-nodeport-service` on port 8080.

