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
    parent: "pod"
weight: 1005
toc: true
---
A Pod is the smallest and simplest unit you can deploy. A Pod may contain one or more tightly coupled containers, sharing the same network namespace and having access to the same storage volumes. 

The containers inside a Pod share the same network IP address and port space, which means they can easily communicate with each other using "localhost." They also share the same lifecycle, so if the Pod is created, all containers inside it start running, and if the Pod is deleted, all the containers are terminated together.

## Example

```yaml
# kubectl run my-nginx --image=nginx --labels="app=my-app,env=prod" --port=80 --env="NGINX_PORT=80" --restart=Always -o yaml --dry-run=client > my-pod.yaml

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

### Kubernetes
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