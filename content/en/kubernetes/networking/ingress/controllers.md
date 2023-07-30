---
title: "Controllers"
description: ""
date: 2023-07-29T17:34:44Z
lastmod: 2023-07-29T17:34:44Z
draft: false
images: []
menu:
  docs:
    parent: "ingress"
type: docs
weight: 1031
toc: true
---

An Ingress Controller is a component responsible for managing and fulfilling ingress rules defined in the Ingress resource. It acts as a reverse proxy and load balancer, enabling external access to services running within the Kubernetes cluster. The Ingress Controller listens to changes in the Ingress resources and configures the underlying load balancer or other networking components accordingly to route incoming traffic to the appropriate services.

## Comparison Table
Below is a comparison table for some popular Kubernetes ingress controllers:
| **Feature** | **Nginx Ingress Controller** | **Traefik** | **HAProxy Ingress** | **Contour** | **Ambassador** | **Kong** |
|---|---|---|---|---|---|---|
| **Supported Protocols** | HTTP, HTTPS, TCP, UDP | HTTP, HTTPS, TCP, UDP | HTTP, HTTPS | HTTP, HTTPS | HTTP, gRPC, WebSocket | HTTP, HTTPS |
| **SSL/TLS Termination** | Yes | Yes | Yes | Yes | Yes | Yes |
| **External Auth Support** | Yes | Yes | Yes | Yes | Yes | Yes |
| **Customizable Routing** | Yes | Yes | Yes | Yes | Yes | Yes |
| **Rewrites & Redirects** | Yes | Yes | Yes | Yes | Yes | Yes |
| **Request Rate Limiting** | Yes | Yes | Yes | Yes | Yes | Yes |
| **WebSockets Support** | Yes | Yes | Yes | Yes | Yes | Yes |
| **Session Affinity** | Yes | Yes | Yes | Yes | Yes | Yes |
| **Host-Based Routing** | Yes | Yes | Yes | Yes | Yes | Yes |
| **Custom Middleware** | No | Yes | Yes | Yes | Yes | Yes |
| **Load Balancing** | Round Robin, Least Connections | Round Robin, Least Connections | Round Robin, Least Connections | Weighted Round Robin | Round Robin, Least Connections | Round Robin, Consistent Hash |
| **Custom Error Pages** | Yes | Yes | Yes | Yes | Yes | Yes |
| **Kubernetes CRD Support** | No | Yes | No | Yes | Yes | Yes |
| **External Service Support** | Yes | Yes | Yes | Yes | Yes | Yes |
| **Active Community** | Yes | Yes | Yes | Yes | Yes | Yes |
| **Documentation Quality** | Good | Good | Good | Good | Good | Good |
| **Complexity** | Moderate | Moderate | Moderate | Moderate | Moderate | Moderate |
| **Performance** | High | High | High | High | High | High |
| **Cloud Provider Support** | Yes | Yes | No | Yes | Yes | Yes |
{.table-sm}

## Deploying Ingress Controllers
### Nginx
This is one of the most widely used Ingress Controllers. It uses the Nginx web server as a reverse proxy to manage the incoming traffic.

The Nginx Ingress Controller has its own Deployment and Service resources to manage the controller's pods and expose them as a service in the cluster.

Create a file named `nginx-ingress-controller.yaml` and add the following content:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ingress-controller
  namespace: nginx-ingress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-ingress-controller
  template:
    metadata:
      labels:
        app: nginx-ingress-controller
    spec:
      containers:
        - name: nginx-ingress-controller
          image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:latest
          args:
            - /nginx-ingress-controller
            - --configmap=$(POD_NAMESPACE)/nginx-configuration
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          ports:
            - name: http
              containerPort: 80
            - name: https
              containerPort: 443
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-ingress-controller
  namespace: nginx-ingress
spec:
  selector:
    app: nginx-ingress-controller
  ports:
    - name: http
      port: 80
      targetPort: http
    - name: https
      port: 443
      targetPort: https
```

Apply the configuration to your cluster:

```bash
kubectl apply -f nginx-ingress-controller.yaml
```

### Traefik
Another popular Ingress Controller that is known for its simplicity and ease of use. It also supports dynamic configuration and integrates well with various cloud providers.

To use Traefik as your ingress controller, you need to deploy it in your Kubernetes cluster and configure it to listen for ingress resources. Below, an example of how to deploy Traefik in Kubernetes is provided.

Create a file named `traefik-ingress-controller.yaml` and add the following content:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: traefik
spec:
  replicas: 1
  selector:
    matchLabels:
      app: traefik
  template:
    metadata:
      labels:
        app: traefik
    spec:
      containers:
      - name: traefik
        image: traefik:v2.5
        ports:
        - name: http
          containerPort: 80
        - name: https
          containerPort: 443
---
apiVersion: v1
kind: Service
metadata:
  name: traefik-service
spec:
  selector:
    app: traefik
  ports:
  - name: http
    port: 80
  - name: https
    port: 443
```

Apply the deployment using the following command:

```bash
kubectl apply -f traefik-ingress-controller.yaml
```

### HAProxy Ingress
HAProxy is a high-performance TCP/HTTP load balancer, and the HAProxy Ingress Controller leverages its capabilities to manage ingress traffic.

To deploy the HAProxy Ingress Controller in your Kubernetes cluster, you'll need to create a deployment and a service. Below are the example YAML files for deploying HAProxy Ingress Controller.

Create a file named `haproxy-ingress-controller.yaml` and add the following content:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: haproxy-ingress-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      app: haproxy-ingress-controller
  template:
    metadata:
      labels:
        app: haproxy-ingress-controller
    spec:
      containers:
      - name: haproxy-ingress-controller
        image: haproxytech/kubernetes-ingress
        args:
        - --default-backend-service=$(POD_NAMESPACE)/default-http-backend
        - --publish-service=$(POD_NAMESPACE)/haproxy-ingress-controller
---
apiVersion: v1
kind: Service
metadata:
  name: haproxy-ingress-controller
spec:
  selector:
    app: haproxy-ingress-controller
  ports:
  - name: http
    port: 80
    targetPort: 80
  - name: https
    port: 443
    targetPort: 443
```

Apply the deployment using the following command:

```bash
kubectl apply -f haproxy-ingress-controller.yaml
```

### Contour
Built on top of Envoy Proxy, Contour provides advanced features and is well-suited for large-scale and complex setups.

### Istio
Although primarily known as a service mesh, Istio can also function as an Ingress Controller, offering advanced traffic management features.

Each of these controllers has its own configuration and setup process. When setting up an Ingress Controller, you need to deploy the controller itself as a separate component within the cluster. The controller watches for changes in the Ingress resources and configures the necessary load balancing and routing rules accordingly. Depending on the Ingress Controller you choose, the setup and configuration may vary slightly.

It's important to note that an Ingress Controller should be complemented by an Ingress Controller Provider to expose the controller to the external network. The Provider could be a cloud provider's load balancer or an external service that manages the external traffic flow to the Ingress Controller.

