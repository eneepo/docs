---
title: "Docker"
description: ""
date: 2023-08-16T06:41:25Z
lastmod: 2023-08-16T06:41:25Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "extra"
weight: 1000
toc: true
---

## Basics
- **Image:** A lightweight, standalone, and executable software package that includes everything needed to run a piece of software, including code, runtime, libraries, and system tools.

- **Container:** An instance of a Docker image that can be run, started, stopped, and deleted. Containers provide isolated environments for running applications.

- **Docker Hub:** A repository of Docker images that you can use and share. It's a centralized platform for finding and sharing container images.

## Dockerfile Directives

- `FROM`: Specifies the base image.
- `RUN`: Runs commands in the image during build.
- `COPY` and `ADD`: Copies files from the host to the image.
- `WORKDIR`: Sets the working directory for subsequent commands.
- `ENV`: Sets environment variables.
- `EXPOSE`: Exposes ports for networking.
- `CMD`: Specifies the default command to run when a container starts.
- `ENTRYPOINT`: Sets the default executable for the container.
- `HEALTHCHECK`: Defines a command to check the container's health in a Dockerfile.

## Creating a Dockerfile
Here's a `Dockerfile`, a simple `app.py` file, and a `requirements.txt` file to create a Docker container running a basic FastAPI app using the `python:3.10-slim` image:

**requirements.txt:**
```
fastapi==0.68.1
uvicorn==0.15.0
```

**app.py:**
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI!"}
```

**Dockerfile:**
```Dockerfile
# Use the official Python 3.10-slim image as the base image
FROM python:3.10-slim

# Set the working directory within the container
WORKDIR /app

# Copy the requirements file into the container at /app
COPY requirements.txt .

# Install the required packages
RUN pip install --no-cache-dir -r requirements.txt

# Copy the current directory contents into the container at /app
COPY . .

# Set the default command to run the FastAPI app
ENTRYPOINT ["uvicorn", "app:app"]

# Default command arguments (can be overridden when running the container)
CMD ["--host", "0.0.0.0", "--port", "80"]

# Healthcheck command to check the status of the FastAPI app
HEALTHCHECK --interval=30s --timeout=3s CMD curl -f http://localhost/ || exit 1
```
In this example, a HEALTHCHECK directive has been added at the end of the Dockerfile. This HEALTHCHECK command checks the status of the FastAPI app every 30 seconds (--interval=30s) by sending an HTTP GET request to http://localhost/. If the app responds within 3 seconds (--timeout=3s), the health check is considered successful. If the app doesn't respond or responds with an error, the health check fails and the container status may be affected accordingly.

To build the Docker image and run the FastAPI app, you would need to have Docker installed on your system. Save the Dockerfile, app.py, and requirements.txt in the same directory, and then open a terminal and navigate to that directory. Run the following commands:

1. Build the Docker image:
   ```
   docker build -t fastapi-app .
   ```

2. Run the Docker container:
   ```
   docker run -d -p 8080:80 fastapi-app
   ```

3. See the docker layers
   ```
   docker history fastapi-app
   ```

The `-p 8080:80` flag maps port 8080 of the host machine to port 80 of the Docker container. You can access the FastAPI app by opening a web browser and navigating to `http://localhost:8080`.

## ENTRYPOINT vs CMD
Both ENTRYPOINT and CMD are instructions in a Dockerfile that are used to specify the command to run when a container is started from an image. However, they have different behaviors and purposes.

### ENTRYPOINT
The ENTRYPOINT instruction sets the main command that is executed when the container starts. It provides an entry point into the container, and any arguments passed to docker run will be treated as arguments to this entry point command. The primary purpose of ENTRYPOINT is to define a consistent command that is difficult to override.

For example, in the context of a FastAPI app, using ENTRYPOINT with the uvicorn command makes sense because uvicorn is the core process that runs the FastAPI app, and you usually don't want to change it.

### CMD
The CMD instruction specifies default arguments for the command defined by the ENTRYPOINT instruction or for the default command if no ENTRYPOINT is specified. If you provide a command when starting a container with docker run, those command-line arguments will override the CMD instruction.

In the context of a FastAPI app, you can use CMD to specify default options for the uvicorn command, such as the host and port to listen on. These defaults can be overridden when starting the container.

### Example

```Dockerfile
FROM Ubuntu
ENTRYPOINT ["sleep"]
CMD ["10"]
```

```shell
$ docker run ubuntu-sleeper 10
$ docker run ubuntu-sleeper 10 --entrypoint sleep_v2
```

Using the above image in a Kubernetes Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  containers:
  - name: ubuntu-sleeper
    image: ubuntu-sleeper
    command: ["sleep_v2"]
    args: ["10"]
```

```shell
k run webapp-green --image=kodekloud/webapp-color --command sleep_v2 -- --color=green
```

## Commands

### Docker Basic Commands
```shell
docker pull <image-name>                                  # Download an image from Docker Hub.
docker build -t <image-name> <path-to-Dockerfile>         # Build a new Docker image using a Dockerfile.
docker run <image-name>                                   # Run a container from an image.
docker ps                                                 # List running containers.
docker ps -a                                              # List all containers (including stopped ones).
docker stop <container-id>                                # Stop a running container.
docker start <container-id>                               # Start a stopped container.
docker restart <container-id>                             # Restart a running or stopped container.
docker rm <container-id>                                  # Remove a container.
docker rmi <image-id>                                     # Remove an image.
```

### Docker Networking
```shell
docker network ls                                         # List Docker networks.
docker network create <network-name>                      # Create a new Docker network.
docker network inspect <network_name>                     # Displays detailed information about a network.
docker network connect <network-name> <container-name>    # Connect a container to a network.
docker network disconnect <network-name> <container-name> # Disconnect a container from a network.
docker run --network=<network-name> <image-name>          # Run a container in a specific network.
```

### Docker Volumes
```shell
docker volume ls                                          # List Docker volumes.
docker volume create <volume-name>                        # Create a new Docker volume.
docker volume inspect <volume_name>                       # Displays detailed information about a volume.
docker volume rm <volume_name>                            # Removes a volume.
docker run -v <volume-name>:<container-path> <image-name> # Mount a volume in a container.
docker run -v <host-path>:<container-path> <image-name>   # Mount a host directory as a volume in a container.
```

### Image Tagging and Pushing
```shell
docker tag <source_image> <new_image_name>:<tag>          # Tags an image with a new name and/or tag.
docker push <image_name>:<tag>                            # Pushes an image to a Docker registry.
```

### Container Interaction
```shell
docker attach <container_id>                              # Attaches to a running container's console.
docker cp <container_id>:<container_path> <host_path>     # Copies files between the host and a container.
docker export <container_id> > <output_tarball.tar>       # Exports a container's filesystem as a tarball.
```

### Miscellaneous
```shell
docker exec -it <container-id> <command>                  # Run a command inside a running container interactively.
docker exec -it <container-id> cat /etc/os-release        # View the container os
docker logs <container-id>                                # View the logs of a container.
docker inspect <container-id>                             # View detailed information about a container.
docker system prune                                       # Remove unused Docker resources (containers, images, networks, volumes).
```

### Docker Registry
```shell
docker login                                             # Log in to a Docker registry.
docker logout                                            # Log out from a Docker registry.
docker search <image_name>                               # Search for images on Docker Hub.
```

### Docker Compose
Docker Compose is a tool for defining and running multi-container Docker applications.
```shell
docker-compose up                                         # Start services defined in a `docker-compose.yml` file.
docker-compose down                                       # Stop and remove containers, networks, and volumes defined in a `docker-compose.yml` file.
docker-compose ps                                         # List running services defined in a `docker-compose.yml` file.
```

### Docker Swarm (for orchestration)
```shell
docker swarm init                                        # Initializes a Docker swarm.
docker service create                                    # Creates a new service.
docker service ls                                        # Lists all services in the swarm.
docker node ls                                           # Lists all nodes in the swarm.
```
