---
title: "Environment Variable"
description: ""
date: 2023-09-02T13:17:18Z
lastmod: 2023-09-02T13:17:18Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1000
---

Environment variables provide a way to pass configuration settings, credentials, or any runtime parameters to containers without hardcoding them within the application code. Environment variables are especially useful for managing sensitive information securely and for achieving configuration flexibility.

## Setting Environment Variables in a Kubernetes Pod

You can define environment variables at different levels within a Kubernetes pod, including:

1. **Container-Level Environment Variables**: These are specific to a single container within a pod.

2. **Pod-Level Environment Variables**: These variables are shared by all containers within a pod.

3. **Secrets and ConfigMaps**: Kubernetes provides resources called Secrets and ConfigMaps that can be used to store and manage environment variables centrally.

Here's an example of defining environment variables at both the container and pod levels.

## Examples

### Example 1

Consider having a docker conatiner simillar to `docker run -e NGINX_HOST="example.com" -e NGINX_PORT="80" nginx`. To pass the same environment variables to Kubernetes pod use the following example:

```yaml
# kubectl run web --image nginx --env="NGINX_HOST=example.com" --env="NGINX_PORT=80"  -o yaml --dry-run=client

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: web
  name: web
spec:
  containers:
  - env:
    - name: NGINX_HOST
      value: example.com
    - name: NGINX_PORT
      value: "80"
    image: nginx
    name: web
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

### Example 2
Let's say you have a simple web application that requires a database connection. You want to pass the database credentials and the web application's configuration via environment variables.

```yaml
# This Kubernetes YAML file defines a Pod resource named "my-app-pod" for deploying an application container.

apiVersion: v1                          # Specifies the API version for this Kubernetes resource.
kind: Pod                               # Indicates that this is a Pod resource.

metadata:                               # Metadata section contains information about the Pod.
  name: my-app-pod                      # Specifies the name of the Pod as "my-app-pod".

spec:                                   # The spec section defines the desired state of the Pod.
  containers:                           # This subsection specifies the containers to run within the Pod.
    - name: web-app-container           # Name of the container within the Pod.
      image: my-web-app-image:v1        # Docker image to use for this container.

      # Environment variables for the container, which can be used to configure the application.
      env:
        - name: DB_HOST                 # Environment variable name: DB_HOST
          value: "db.example.com"       # Value for DB_HOST
        - name: DB_PORT                 # Environment variable name: DB_PORT
          value: "5432"                 # Value for DB_PORT

        # The following environment variables retrieve their values from a Kubernetes Secret.
        - name: DB_USER                 # Environment variable name: DB_USER
          valueFrom:
            secretKeyRef:
              name: db-creds-secret     # Name of the Secret resource.
              key: username             # Key within the Secret to get the value from.

        - name: DB_PASSWORD             # Environment variable name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-creds-secret     # Name of the Secret resource.
              key: password             # Key within the Secret to get the value from.

  # Defines the volumes available to the Pod.
  volumes:
    - name: config-volume               # Name of the volume.
      configMap:                        # Specifies that this volume is sourced from a ConfigMap.
        name: app-config                # Name of the ConfigMap from which this volume is populated.

```

You would need to create the "db-creds-secret" and "app-config" ConfigMap beforehand using the appropriate data. For example, you can create the "db-creds-secret" with the following command:

```shell
kubectl create secret generic db-credentials-secret --from-literal=username=myuser --from-literal=password=mypassword
```

## Commands

```shell
# Run a Docker container with the NGINX image, setting environment variables for NGINX_HOST and NGINX_PORT.
docker run -e NGINX_HOST="example.com" -e NGINX_PORT="80" nginx

# Create a Kubernetes pod named 'web' using the NGINX container image, and set environment variables NGINX_HOST and NGINX_PORT.
kubectl run web --image nginx --env="NGINX_HOST=example.com" --env="NGINX_PORT=80"
```