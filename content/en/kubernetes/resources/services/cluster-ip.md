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
    parent: "networking"
weight: 1110
toc: true
---

A ClusterIP service exposes the service on a cluster-internal IP address, making it accessible only within the cluster. This type of service is often used for communication between different components of an application within the cluster.

Below is an example YAML file for a ClusterIP service along with comments explaining each section:

```yaml
# kubectl create service clusterip my-clusterip-service --tcp=80:8080 --dry-run=client -o yaml > my-clusterip-service.yaml

apiVersion: v1              # Specifies the Kubernetes API version being used for this resource.
kind: Service               # Specifies the type of Kubernetes resource, in this case, a Service.

metadata:                   # Metadata contains information about the Service, such as its name and labels.
  creationTimestamp: null     # The creationTimestamp will be automatically populated by Kubernetes upon creation.

  labels:
    app: my-clusterip-service # Labels used to identify and group related resources.
  name: my-clusterip-service  # The name of the Service, which can be used to reference it within the cluster.

spec:                       # Spec defines the desired state of the Service.
  
  ports:                      # Ports section specifies the ports that this Service should listen on.
  - name: 80-8080             # A human-readable name for the port configuration.
    port: 80                  # Port on which the Service should listen.
    protocol: TCP             # The protocol used for this port (TCP in this case).
    targetPort: 8080          # The port on the pods that traffic should be forwarded to.

  selector:
    app: my-app               # Selects the Pods that this Service should route traffic to based on their labels.

  type: ClusterIP             # Type defines the type of Service. ClusterIP exposes the Service only within the cluster.

status:                     # Status section is automatically populated by Kubernetes and represents the current state of the Service.
  loadBalancer: {}            # In this case, there's no external load balancer associated with the Service.
```

In this example, the service is named `my-clusterip-service`. It uses the selector `app: my-app` to identify the pods associated with this service. Any pod with the label `app: my-app` will be part of this service. The service exposes port 80 on the ClusterIP address, and any traffic sent to this port will be forwarded to port 8080 on the pods that have the label `app: my-app`.

Since the `type` is specified as `ClusterIP`, this service will be accessible only within the cluster through its internal IP address or the service name. Other services or external clients cannot access it directly. If you need external access, you would use a different service type like NodePort or LoadBalancer.