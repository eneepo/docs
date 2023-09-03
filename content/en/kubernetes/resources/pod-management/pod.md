---
title: "Pod"
description: ""
date: 2023-07-16T09:05:58+02:00
lastmod: 2023-07-16T09:05:58+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "pod-management"
weight: 1000
toc: true
---
A Pod is the smallest and simplest unit you can deploy. A Pod may contain one or more tightly coupled containers, sharing the same network namespace and having access to the same storage volumes. 

The containers inside a Pod share the same network IP address and port space, which means they can easily communicate with each other using "localhost." They also share the same lifecycle, so if the Pod is created, all containers inside it start running, and if the Pod is deleted, all the containers are terminated together.

## Example

```yaml
# kubectl run my-nginx --image=nginx --labels="app=my-app,env=prod" --port=80 --env="NGINX_PORT=80" --restart=Always

apiVersion: v1              # Specifies the Kubernetes API version being used for this resource.
kind: Pod                   # Indicates that this YAML defines a Kubernetes Pod resource.

metadata:                   # Metadata section contains information about the Pod.
  creationTimestamp: null     # The timestamp when this Pod was created, initially set to null.
  labels:                     # Labels are key-value pairs used to identify and categorize the Pod.
    app: my-app               # Label indicating the application name is 'my-app'.
    env: prod                 # Label indicating the environment is 'prod'.
  name: my-nginx              # The name assigned to this Pod.

spec:                       # The specification section defines the desired state of the Pod.
  containers:                 # List of containers running inside this Pod.
  - env:                      # Environment variables to be set within the container.
    - name: NGINX_PORT        # Name of the environment variable.
      value: "80"             # Value assigned to the NGINX_PORT environment variable.
    image: nginx              # The Docker image to be used for this container.
    name: my-nginx            # Name of this container within the Pod.
    ports:                    # List of ports to be exposed by this container.
    - containerPort: 80       # The container will listen on port 80.
    resources: {}             # Resource requests and limits can be specified here.
  dnsPolicy: ClusterFirst     # Defines the DNS resolution policy for this Pod.
  restartPolicy: Always       # Specifies the Pod's restart policy, which is set to "Always."

status: {}                    # The current status of the Pod (empty in this YAML as it's an initial state).
```

## CMD vs ENTRYPOINT
`CMD` commands are ignored by daemon when there are parameters stated within the docker run command while `ENTRYPOINT` instructions are not ignored but instead are appended as command line parameters by treating those as arguments of the command.

### `CMD`
Sets default parameters that can be overridden from the Docker command line interface (CLI) while running a docker container.

```dockerfile
FROM ubuntu
CMD ["echo", "Hello World"]
```

```bash
$ docker build -t cmd-instructions .
[+] Building 0.0s (5/5) FINISHED                           docker:desktop-linux

$ docker run cmd-instructions
Hello World

$ docker run cmd-instruction echo "Hola!"
Hola!

$ docker run cmd-instructions printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=bd90afe95bd8
HOME=/root
```

### `ENTRYPOINT`
Sets default parameters that cannot be overridden while executing Docker containers with CLI parameters.

```dockerfile
FROM ubuntu
ENTRYPOINT ["echo", "Hello World"]
```

```bash
$ docker build -t entrypoint-instructions .
[+] Building 0.0s (5/5) FINISHED                           docker:desktop-linux

$ docker run entrypoint-instructions
Hello World

$ docker run entrypoint-instruction echo "Hola!"
Hello World echo Hola!

$ docker run entrypoint-instructions printenv
Hello World printenv

$ docker run --entrypoint printenv entrypoint-instructions
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=d21eb640262d
HOME=/root
```

### Using `CMD` and `ENTRYPOINT` together

```dockerfile
FROM ubuntu
ENTRYPOINT ["echo", "Hello"]
CMD ["World!"]
```

```bash
$ docker build -t entrypoint-cmd .
[+] Building 0.0s (5/5) FINISHED                           docker:desktop-linux

$ docker run entrypoint-cmd
Hello World!

$ docker run entrypoint-cmd Eneepo!
Hello Eneepo!

$ docker run entrypoint-instructions printenv
Hello World printenv

# Sleeps for 5 seconds. Equivalent of running `sleep 10`
$ docker run --entrypoint sleep entrypoint-cmd 5
```

### Equivalents in Kubernetes
In Kubernetes:
* Use `command` for `ENTRYPOINT`
* use `args` for `CMD`

```dockerfile
FROM Ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
```

```shell
$ docker build ubuntu-sleeper .
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  containers:
  - name: ubuntu-sleeper
    image: ubuntu-sleeper
    command: ["sleep"]  
    args: ["10"]
```

Running the above pod is simillar to running:
```bash
$ docker run ubuntu-sleeper 10
```

#### Change `command` and `args` when creating pod

```bash
# Start the nginx pod using the default command, but use custom arguments (arg1 .. argN) for that command
# kubectl run nginx --image=nginx -- <arg1> <arg2> ... <argN>
$ kubectl run ubuntu-sleeper --image=ubuntu-sleeper-pod -- 5

# Start the nginx pod using a different command and custom arguments
# kubectl run nginx --image=nginx --command -- <cmd> <arg1> ... <argN>
$ kubectl run ubuntu-sleeper --image=ubuntu-sleeper-pod --command -- echo "Hello World!"
```


## Pod Conditions
Pods have a variety of conditions that indicate their status and health. These conditions are represented by the status of the conditions field within the Pod object. The following are some of the common conditions you might encounter:

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

## Multi-container

Multi-container design patterns are used to define how multiple containers within a pod interact and collaborate with each other to accomplish a specific task. These design patterns enable efficient communication and resource sharing between containers within a pod. Here are some commonly used multi-container design patterns in Kubernetes:

### Sidecar Pattern
In this pattern, a sidecar container is added to a pod to enhance or extend the functionality of the main container. The sidecar container runs alongside the main container and shares the same lifecycle, network, and storage resources. It can perform tasks such as logging, monitoring, security, or proxying for the main container.

### Ambassador Pattern
The ambassador pattern is used to offload network-related concerns from the main container to a separate ambassador container within the same pod. The ambassador container acts as a proxy and handles tasks such as load balancing, SSL termination, or service discovery, allowing the main container to focus on its core functionality.

### Adapter Pattern
The adapter pattern involves using an adapter container to convert the output of one container into a format that another container can understand. This pattern is commonly used when integrating containers with different protocols or data formats, allowing them to communicate effectively.

### Sidecar Injector Pattern
The sidecar injector pattern is used to dynamically inject a sidecar container into a pod at runtime based on specific conditions or requirements. This pattern is often used for tasks such as injecting a container for logging or injecting a container with additional security features based on policies or configurations.

### Init Container Pattern
In this pattern, an init container is added to a pod to perform initialization tasks before the main container starts. Init containers are executed sequentially and can be used to set up prerequisites, populate shared volumes, or perform other one-time setup tasks.


An init container is a special type of container that runs and completes before the main application containers start. Init containers are designed to perform initialization tasks, such as setting up configuration files, populating a shared database, or fetching required resources, before the main containers are launched. They help ensure that the application containers start in a known and ready state.

Init containers run to completion before any of the application containers start, and they share the same pod and network namespace. The main containers in the pod do not start until all init containers have successfully completed their execution. If any init container fails, Kubernetes restarts the pod and retries running the init containers until they all succeed.

Init containers are defined in the pod specification, alongside the main containers, and they are executed in the order they are defined. Each init container runs one at a time, and only when the previous init container has successfully completed.

Here's an example of a pod specification with init containers:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: main-container
    image: my-main-image
    # ...
  initContainers:
  - name: init-container-1
    image: my-init-image-1
    # ...
  - name: init-container-2
    image: my-init-image-2
    # ...

```

### Adapterless Pattern
The adapterless pattern is an alternative to the adapter pattern, where containers communicate directly with each other without the need for an intermediary adapter container. This pattern is suitable when containers can understand and work with each other's protocols or data formats without any conversion.

These are some of the commonly used multi-container design patterns in Kubernetes. Each pattern has its own benefits and use cases, allowing developers to design complex, scalable, and modular applications within Kubernetes clusters.

## Commands

```shell
# Create a resource from a file
kubectl create -f pod-definition.yaml

# Apply a configuration to a pod by file name
kubectl apply -f pod-definition.yaml

# Display one or many pods.
kubectl get pods

# Prints a table of the most important information about the specified resources.
kubectl describe pod myapp-pod

# Quickly generate a new pod definition 
kubectl run my-pod --image nginx --dry-run=client -o yaml > pod-definition.yaml

# Start the nginx pod using the default command, but use custom arguments (arg1 .. argN) for that command
kubectl run nginx --image=nginx -- <arg1> <arg2> ... <argN>

# Start the nginx pod using a different command and custom arguments
kubectl run nginx --image=nginx --command -- <cmd> <arg1> ... <argN>

# Generate a pod definition out of an exisiting pod
kubectl get pod mu-pod -o yaml > pod-definition-get.yaml
```