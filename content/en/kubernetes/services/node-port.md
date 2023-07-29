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
    parent: "services"
weight: 1015
toc: true
---
A NodePort service exposes the service on a static port on each node's IP. This allows the service to be accessed from outside the cluster using `NodeIP:NodePort`. It is useful when you need to expose a service externally and do not want to use an external load balancer.

Below is an example YAML file for a NodePort service along with comments explaining each section:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service     # The name of the service
spec:
  selector:
    app: my-app                # Selectors to identify the pods belonging to this service
  ports:
    - protocol: TCP
      port: 80                 # Port on the NodePort service that will be exposed
      targetPort: 8080         # Port on the pods that the service will route traffic to
      nodePort: 30080          # Port on each node that the service will be accessible from
  type: NodePort              # The type of service, in this case, it's NodePort
```

In this example, the service is named `my-nodeport-service`. It uses the selector `app: my-app` to identify the pods associated with this service. Any pod with the label `app: my-app` will be part of this service.

The service exposes port 80 on each node's IP, and any traffic sent to this port will be forwarded to port 8080 on the pods that have the label `app: my-app`.

The `nodePort` field specifies the static port number (in this example, 30080) on each node that will be used to access the service externally. This port number must be in the valid range (30000-32767) specified for NodePort services.

Note that the external access will be possible through any node's IP address, followed by the specified `nodePort` (e.g., `NodeIP:30080`).

Keep in mind that using a NodePort service may expose your application to the public internet directly, and you might want to consider additional security measures like network policies or using an Ingress controller if you need more advanced routing capabilities.