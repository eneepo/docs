---
title: "Ingress Resource"
description: ""
date: 2023-07-29T19:56:17Z
lastmod: 2023-07-29T19:56:17Z
draft: false
images: []
menu:
  docs:
    parent: "ingress"
type: docs
weight: 1035
toc: true
---

There are several kinds of Ingress Resources that you can use to configure and manage the routing of incoming traffic to your services. Each kind of Ingress Resource provides different capabilities and features to suit various use cases. Let's go through some common types of Ingress Resources with example YAML files for each:

## Examples
### Basic Ingress Resource

The basic Ingress resource defines simple host-based routing rules. It allows you to route traffic based on the requested hostname and path.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: basic-ingress
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /app
            pathType: Prefix
            backend:
              service:
                name: example-app-service
                port:
                  number: 80
```

In this example, the Ingress resource named "basic-ingress" routes requests to `example.com/app` to the "example-app-service" on port 80.

### TLS Ingress Resource

The TLS Ingress resource extends the basic Ingress to support SSL/TLS termination for encrypted traffic.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-ingress
spec:
  rules:
    - host: secure.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: secure-app-service
                port:
                  number: 443
  tls:
    - hosts:
        - secure.example.com
      secretName: tls-secret
```

In this example, the Ingress resource named "tls-ingress" routes requests to `secure.example.com` to the "secure-app-service" on port 443 and terminates SSL/TLS using the secret "tls-secret."

### Name-Based Virtual Hosting

The Name-Based Virtual Hosting Ingress resource enables routing based on different hostnames.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: virtual-host-ingress
spec:
  rules:
    - host: app1.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app1-service
                port:
                  number: 80
    - host: app2.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app2-service
                port:
                  number: 80
```

In this example, the Ingress resource named "virtual-host-ingress" routes requests to `app1.example.com` to "app1-service" and requests to `app2.example.com` to "app2-service," both on port 80.

### Path-Based Routing

The Path-Based Routing Ingress resource enables routing based on different paths for the same hostname.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: path-routing-ingress
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /app1
            pathType: Prefix
            backend:
              service:
                name: app1-service
                port:
                  number: 80
          - path: /app2
            pathType: Prefix
            backend:
              service:
                name: app2-service
                port:
                  number: 80
```

In this example, the Ingress resource named "path-routing-ingress" routes requests to `/app1` to "app1-service" and requests to `/app2` to "app2-service," both for the hostname `example.com` on port 80.

These examples demonstrate some of the common kinds of Ingress Resources in Kubernetes. Depending on your needs, you can use different Ingress types to customize and manage the routing of incoming traffic to your applications within the cluster.

Sure! Here's a Kubernetes `kubectl` cheatsheet for working with Ingress resources:

## Commands

### General

```bash
kubectl get ingresses                           # Get Ingresses
kubectl describe ingress <ingress_name>         # Describe Ingress
kubectl apply -f <ingress_yaml_file>            # Create or Apply an Ingress from YAML
kubectl edit ingress <ingress_name>             # Edit an Ingress
kubectl delete ingress <ingress_name>           # Delete an Ingress
```

### Imperative
Certainly! Below are some examples of using `kubectl create ingress` with comments to explain each part:

#### Basic Ingress with Host-Based Routing

```bash
kubectl create ingress basic-ingress --rule="host=example.com,http.paths=/app,backend.service.name=example-app-service,backend.service.port=80"
```

Explanation:
- `create ingress`: Creates an Ingress resource.
- `basic-ingress`: Name of the Ingress resource.
- `--rule`: Defines the routing rule with comma-separated key-value pairs.
  - `host=example.com`: Requests with the host "example.com."
  - `http.paths=/app`: Requests with the path prefix "/app."
  - `backend.service.name=example-app-service`: Routes to the "example-app-service" Kubernetes service.
  - `backend.service.port=80`: Routes to port 80 of the "example-app-service."

#### Ingress with TLS Termination

```bash
kubectl create ingress tls-ingress --rule="host=secure.example.com,http.paths=/,backend.service.name=secure-app-service,backend.service.port=443" --tls=secure.example.com=tls-secret
```

Explanation:
- `create ingress`: Creates an Ingress resource.
- `tls-ingress`: Name of the Ingress resource.
- `--rule`: Defines the routing rule with comma-separated key-value pairs.
  - `host=secure.example.com`: Requests with the host "secure.example.com."
  - `http.paths=/`: Requests with any path.
  - `backend.service.name=secure-app-service`: Routes to the "secure-app-service" Kubernetes service.
  - `backend.service.port=443`: Routes to port 443 of the "secure-app-service."
- `--tls`: Specifies the TLS termination configuration.
  - `secure.example.com`: Hostname for which the TLS termination should be applied.
  - `tls-secret`: Kubernetes Secret containing the TLS certificate and private key.

#### Name-Based Virtual Hosting

```bash
kubectl create ingress virtual-host-ingress --rule="host=app1.example.com,http.paths=/,backend.service.name=app1-service,backend.service.port=80" --rule="host=app2.example.com,http.paths=/,backend.service.name=app2-service,backend.service.port=80"
```

Explanation:
- `create ingress`: Creates an Ingress resource.
- `virtual-host-ingress`: Name of the Ingress resource.
- `--rule`: Defines multiple routing rules for different hostnames.
  - Rule 1: Requests with the host "app1.example.com" will route to the "app1-service" on port 80.
  - Rule 2: Requests with the host "app2.example.com" will route to the "app2-service" on port 80.

#### Path-Based Routing

```bash
kubectl create ingress path-routing-ingress --rule="host=example.com,http.paths=/app1,backend.service.name=app1-service,backend.service.port=80" --rule="host=example.com,http.paths=/app2,backend.service.name=app2-service,backend.service.port=80"
```

Explanation:
- `create ingress`: Creates an Ingress resource.
- `path-routing-ingress`: Name of the Ingress resource.
- `--rule`: Defines multiple routing rules for different paths under the same hostname.
  - Rule 1: Requests with the path prefix "/app1" will route to the "app1-service" on port 80.
  - Rule 2: Requests with the path prefix "/app2" will route to the "app2-service" on port 80.

These examples demonstrate different use cases for creating Ingress resources with `kubectl create ingress`. You can customize the routing rules according to your application's needs using the appropriate flags and options.