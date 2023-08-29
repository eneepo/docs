---
title: "Conditions, Propes"
description: ""
date: 2023-07-26T07:29:49+02:00
lastmod: 2023-07-26T07:29:49+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "pod"
weight: 1050
toc: true
---

## Pod Conditions
Pods in Kubernetes have a variety of conditions that indicate their status and health. These conditions are represented by the status.conditions field within the Pod object. The following are some of the common conditions you might encounter:

### PodScheduled
This condition indicates whether the pod has been scheduled to run on a node. If the condition is False, it means the pod is not yet scheduled.

### Initialized
This condition indicates whether all the containers within the pod have been successfully initialized. If the condition is False, it means one or more containers failed to initialize.

### Ready
This condition indicates whether all the containers within the pod are ready to accept traffic. If the condition is False, it means one or more containers are not ready.

### ContainersReady
This condition is similar to the "Ready" condition but specifically represents whether all containers in the pod are ready. If the condition is False, it means one or more containers are not ready.

### PodInitialized
This condition indicates whether the pod initialization process has completed. It is set to True when all the containers in the pod have been successfully initialized.

### Unschedulable
This condition indicates whether the pod is currently unschedulable. If the condition is True, it means the pod cannot be scheduled to run on any node.

## Readiness Probe
When a pod is created or updated, Kubernetes uses the ReadinessProbe to determine if the container is ready to start receiving network traffic. The probe periodically checks the health of the container by sending requests to a specified endpoint within the container. The endpoint can be an HTTP or TCP endpoint.

There are three types of ReadinessProbes available in Kubernetes:

### HTTP GET Probe
It sends an HTTP GET request to the specified path/port of the container. If the response code falls within a specific range (default: 200-399), the container is considered ready.
```yaml
readinessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```
In this example, the probe sends an HTTP GET request to /healthz on port 8080. It will start probing after an initial delay of 5 seconds and repeat the probe every 10 seconds.


### TCP Socket Probe
It attempts to open a TCP connection to the specified port of the container. If the connection is successful, the container is considered ready.
```yaml
readinessProbe:
  tcpSocket:
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```
In this example, the probe attempts to establish a TCP connection to port 8080. It will start probing after an initial delay of 10 seconds and repeat the probe every 5 seconds.


### Exec Probe
It executes a specified command inside the container and considers the container ready if the command exits with a zero status code.
```yaml
readinessProbe:
  exec:
    command:
      - cat
      - /tmp/ready
  initialDelaySeconds: 15
  periodSeconds: 20
```
In this example, the probe runs the command cat /tmp/ready inside the container. If the command succeeds (returns zero), the container is considered ready. The probe starts after an initial delay of 15 seconds and repeats every 20 seconds.


### Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: my-image:latest
      ports:
        - containerPort: 8080
      readinessProbe:
        httpGet:
          path: /health
          port: 8080
        initialDelaySeconds: 10
        periodSeconds: 5
        failureThreshold: 8
```
In this example, the pod specification includes a single container named my-container, which exposes port 8080. The readinessProbe section defines an HTTP GET probe, which checks the /health path on port 8080 of the container. The initialDelaySeconds field specifies a delay of 10 seconds before the first readiness check is performed, and periodSeconds sets the frequency of subsequent checks to every 5 seconds.

During deployment, Kubernetes will monitor the container's readiness by sending HTTP GET requests to /health on port 8080. If the container responds with a success status (HTTP 2xx), it will be considered ready. If the container fails to respond successfully within the configured time frame or returns an error status (HTTP 4xx or 5xx), it will be marked as not ready, and Kubernetes will delay routing traffic to that container.

By utilizing readiness probes, Kubernetes ensures that traffic is only sent to containers that are fully operational and ready to handle requests, helping to maintain the stability and availability of your application.

## Liveness Probe
A LivenessProbe is a mechanism used to determine if a container in a Pod is still running and functioning correctly. It is a crucial component for maintaining the availability of applications running in Kubernetes clusters. The LivenessProbe periodically sends requests to the container and evaluates the response to determine its health status. If the probe fails, Kubernetes takes remedial action, such as restarting the container or terminating the Pod.

There are three types of LivenessProbes available in Kubernetes:

### HTTP LivenessProbe
This probe performs an HTTP GET request on a specified endpoint within the container and checks if it returns a successful response code (2xx or 3xx). For example, you can configure a LivenessProbe to check if a web server is responding correctly. If the probe fails, Kubernetes restarts the container.
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```

### TCP LivenessProbe
This probe attempts to open a TCP connection to a specific port on the container. If the connection is successful, the probe considers the container as healthy. It is useful for checking if a non-HTTP service is running correctly.
```yaml
livenessProbe:
  tcpSocket:
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 30
```


### Exec LivenessProbe
This probe runs a command inside the container and checks if it returns a zero exit code. It is useful for custom health checks that cannot be performed using HTTP or TCP probes.
```yaml
livenessProbe:
  exec:
    command:
      - sh
      - -c
      - cat /tmp/healthy
  initialDelaySeconds: 15
  periodSeconds: 60
```

These examples illustrate how to configure LivenessProbes in a Kubernetes Pod's YAML definition. The initialDelaySeconds specifies the number of seconds after the container starts before the first probe is performed, and periodSeconds defines the interval between subsequent probe checks.

By using LivenessProbes effectively, Kubernetes can detect and automatically recover from container failures, ensuring high availability of your applications.

### Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: my-image:latest
      ports:
        - containerPort: 8080
      livenessProbe:
        httpGet:
          path: /health
          port: 8080
        initialDelaySeconds: 10
        periodSeconds: 5
        failureThreshold: 8
```