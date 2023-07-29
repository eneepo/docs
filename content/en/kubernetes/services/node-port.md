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

## Delcarative
Below is an example YAML file for a NodePort service along with comments explaining each section:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service    # The name of the service
spec:
  selector:
    app: my-app                # Selectors to identify the pods belonging to this service
  ports:
    - protocol: TCP
      port: 80                 # Port on the NodePort service that will be exposed
      targetPort: 8080         # Port on the pods that the service will route traffic to
      nodePort: 30080          # Port on each node that the service will be accessible from
  type: NodePort               # The type of service, in this case, it's NodePort
```

## Imperative
Now, here's the imperative command to create the same NodePort service:

```bash
kubectl create service nodeport my-nodeport-service --tcp=80:8080 --node-port=30080 --selector=app=my-app
```

Explanation of the command:

- `kubectl create service nodeport`: This part of the command tells Kubernetes to create a NodePort service.
- `my-nodeport-service`: The name given to the service.
- `--tcp=80:8080`: Specifies that incoming traffic on port 80 of the service should be forwarded to port 8080 on the pods.
- `--node-port=30080`: Specifies the static port on each node to access the service from outside the cluster.
- `--selector=app=my-app`: Sets the selector to identify the pods that should be part of this service. Replace `app=my-app` with the appropriate label selector for your pods.

### Generating the yaml file
```bash
kubectl get service my-nodeport-service --dry-run=client -o yaml > my-nodeport-service.yaml
```
