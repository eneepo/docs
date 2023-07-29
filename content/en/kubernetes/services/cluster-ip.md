---
title: "Cluster Ip"
description: ""
date: 2023-07-29T15:56:54Z
lastmod: 2023-07-29T15:56:54Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "services"
weight: 1005
toc: true
---
A ClusterIP service exposes the service on a cluster-internal IP address, making it accessible only within the cluster. This type of service is often used for communication between different components of an application within the cluster.

Below is an example YAML file for a ClusterIP service along with comments explaining each section:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-clusterip-service   # The name of the service
spec:
  selector:
    app: my-app               # Selectors to identify the pods belonging to this service
  ports:
    - protocol: TCP
      port: 80                # Port on the ClusterIP service that will be exposed
      targetPort: 8080        # Port on the pods that the service will route traffic to
  type: ClusterIP             # The type of service, in this case, it's ClusterIP
```

In this example, the service is named `my-clusterip-service`. It uses the selector `app: my-app` to identify the pods associated with this service. Any pod with the label `app: my-app` will be part of this service.

The service exposes port 80 on the ClusterIP address, and any traffic sent to this port will be forwarded to port 8080 on the pods that have the label `app: my-app`.

Since the `type` is specified as `ClusterIP`, this service will be accessible only within the cluster through its internal IP address. Other services or external clients cannot access it directly. If you need external access, you would use a different service type like NodePort or LoadBalancer.