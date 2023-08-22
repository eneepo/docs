---
title: "Containers"
description: ""
date: 2023-08-22T06:43:47Z
lastmod: 2023-08-22T06:43:47Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "architecture"
weight: 1100
toc: true
---

## CRI
The Container Runtime Interface (CRI) in Kubernetes is an essential component that serves as an interface between Kubernetes and the container runtime that actually runs containers on a node in the cluster. The CRI was introduced to provide flexibility and compatibility between Kubernetes and various container runtimes, allowing Kubernetes to support multiple container runtimes seamlessly.

Here's a breakdown of its key components:

1. **Kubelet**: The Kubelet is an essential component of a Kubernetes node. It's responsible for managing containers on that node. However, it doesn't interact directly with the container runtime. Instead, it interacts with the CRI to perform container-related tasks.

2. **Container Runtime**: The container runtime is the software responsible for running containers. Docker, containerd, and cri-o are examples of container runtimes. Each of these runtimes has its own API and mechanisms for managing containers.

3. **Container Runtime Interface (CRI)**: The CRI acts as an intermediary layer between Kubernetes components (like Kubelet) and the container runtime. It standardizes how Kubernetes interacts with the container runtime, abstracting the differences between various runtimes.

The CRI provides two primary components:

- **CRI API**: This is the API that Kubernetes components use to communicate with the container runtime. Kubernetes components like Kubelet send requests to the CRI API to create, run, and manage containers.

- **CRI Implementation**: The CRI implementation is part of the container runtime and translates the CRI API requests into actions the specific container runtime understands. For instance, it can take a request from Kubelet to start a container and translate that request into a command that Docker or containerd understands.

By separating the CRI from the Kubernetes components and container runtimes, Kubernetes can support various container runtimes without having to make significant changes to its core codebase. This modularity allows users to choose the container runtime that best fits their needs while still benefiting from Kubernetes' orchestration capabilities.

In summary, the Container Runtime Interface (CRI) in Kubernetes is a crucial abstraction layer that standardizes interactions between Kubernetes and container runtimes, making it easier to support different runtimes and providing flexibility to Kubernetes users.

## Container Runtimes
Two common container runtimes are `CRI-O` and `Containerd`. They have similar roles in the Kubernetes ecosystem, but there are some key differences between them. Here's a comparison of CRI-O and Containerd:

1. **Focus and Design Philosophy**:
   - **CRI-O**: CRI-O is designed specifically for Kubernetes. Its primary focus is on providing a lightweight, Kubernetes-compatible container runtime. It aims to minimize extraneous features and complexity.
   - **Containerd**: Containerd is a more general-purpose container runtime. While it can be used with Kubernetes, it is not designed exclusively for it. Containerd provides a broader set of functionalities compared to CRI-O.

2. **Kubernetes Compatibility**:
   - **CRI-O**: CRI-O is tightly integrated with Kubernetes and implements the Container Runtime Interface (CRI) standard. It is well-suited for Kubernetes clusters and is a direct choice for many Kubernetes deployments.
   - **Containerd**: Containerd also implements CRI, making it compatible with Kubernetes. However, it is more versatile and can be used with other container orchestration platforms as well.

3. **Features and Extensibility**:
   - **CRI-O**: CRI-O aims to be minimalistic and focuses on essential container runtime features. It may have fewer features compared to Containerd, as it prioritizes simplicity and Kubernetes compatibility.
   - **Containerd**: Containerd provides a wider range of features, making it suitable for various container use cases. It includes features like content distribution, image management, and snapshots, which are not all directly related to running containers but can be useful in broader container environments.

4. **Community and Ecosystem**:
   - **CRI-O**: CRI-O is part of the Cloud Native Computing Foundation (CNCF) and has a strong focus on Kubernetes. It has a dedicated community of contributors and users within the Kubernetes ecosystem.
   - **Containerd**: Containerd is also part of the CNCF and has a broader community, as it can be used with different container orchestration platforms, not just Kubernetes.

5. **Security and Isolation**:
   - **CRI-O**: CRI-O emphasizes security and isolation. It employs technologies like systemd-nspawn and runc to create and manage containers securely, ensuring strong isolation.
   - **Containerd**: Containerd also provides isolation but does not have the same level of emphasis on security as CRI-O.

6. **OCI Compatibility**:
   - **CRI-O**: Like Containerd, CRI-O is compatible with the Open Container Initiative (OCI) standards, ensuring that it can work with OCI-compliant container images and runtimes.
   - **Containerd**: Containerd played a significant role in shaping the OCI standard and is considered a reference implementation of OCI.

In summary, the choice between CRI-O and Containerd depends on your specific use case and requirements. If you are primarily using Kubernetes and value a minimalistic, Kubernetes-focused runtime, CRI-O may be a better fit. If you need a more versatile container runtime that can be used in a variety of container orchestration scenarios, including Kubernetes, Containerd might be the choice. Ultimately, both are capable container runtimes with active communities and can work effectively in Kubernetes environments.

### Containerd

[Containerd](https://containerd.io/) is an industry-standard core container runtime that provides the basic functionality required for running containers. It is used as the container runtime in Kubernetes, specifically starting from Kubernetes version 1.24 and onwards, Kubernetes releases before v1.24 included a direct integration with Docker Engine, using a component named dockershim. That special direct integration is no longer part of Kubernetes (this removal was announced as part of the v1.20 release).

Here are some key points about Containerd and when Kubernetes uses it:

1. **Containerd Functionality**: Containerd provides the essential container runtime functionalities, such as container lifecycle management (creating, starting, stopping, and deleting containers), image management (pulling, pushing, and managing container images), and networking for containers. It is a low-level runtime, and it focuses on container basics without including additional features like Docker's higher-level tools (e.g., Docker Compose, Docker Swarm).

2. **Kubernetes Compatibility**: Kubernetes introduced support for Containerd as a container runtime starting from version 1.20. This means that Kubernetes can use Containerd as the underlying runtime for running containers on worker nodes.

3. **Docker Deprecation**: Kubernetes originally relied heavily on Docker as the default container runtime. However, due to changes in the container ecosystem and the desire for greater flexibility and standardization, Kubernetes gradually deprecated direct Docker support. This led to the adoption of the Container Runtime Interface (CRI) as the standardized way for Kubernetes to interact with container runtimes.

4. **CRI Compatibility**: Containerd, like other container runtimes (e.g., cri-o), implements the CRI, which allows Kubernetes components, particularly the Kubelet, to interact with it using the CRI API. This abstraction layer makes it possible to switch container runtimes seamlessly without significant changes to the Kubernetes core.

5. **Advantages**: Containerd is known for its lightweight and minimalistic design. It is well-suited for scenarios where you want a container runtime that focuses solely on running containers efficiently. Its simplicity and adherence to container standards make it a good choice for Kubernetes deployments.

In summary, Containerd is used in Kubernetes as one of the supported container runtimes, and it offers a balance between simplicity and functionality. Kubernetes users may choose to use Containerd when they prefer a straightforward, standards-compliant runtime that aligns well with Kubernetes' Container Runtime Interface (CRI).

### CRI-O
CRI-O is an open-source container runtime that is designed specifically for Kubernetes. It's an alternative to other container runtimes like Docker or containerd. CRI-O is part of the Cloud Native Computing Foundation (CNCF) and aims to provide a lightweight and secure runtime environment for containers in Kubernetes clusters.

Here are some key points about CRI-O in Kubernetes:

1. **Kubernetes Container Runtime**: CRI-O is used as the container runtime in Kubernetes, serving the same role as Docker or containerd. It is responsible for running containers on worker nodes in a Kubernetes cluster.

2. **CRI Compatibility**: Like other container runtimes, CRI-O implements the Container Runtime Interface (CRI). This standardizes how Kubernetes components, particularly the Kubelet, communicate with the container runtime. The CRI allows Kubernetes to remain agnostic about the underlying container runtime, making it easier to switch runtimes without altering Kubernetes core code.

3. **Focused on Kubernetes**: CRI-O is designed with Kubernetes in mind. This means it doesn't include some of the additional features that Docker provides, such as Docker Compose or Docker Swarm orchestration. Instead, it concentrates on core container functionalities that Kubernetes requires.

4. **Security and Isolation**: CRI-O emphasizes security and container isolation. It employs technologies like systemd-nspawn and runc to create and manage containers securely, ensuring that containers are isolated from each other and from the host system.

5. **OCI Compatibility**: CRI-O is compatible with the Open Container Initiative (OCI) standards, ensuring that it can work with OCI-compliant container images and runtimes.

6. **OCI Container Image Support**: It can pull, push, and manage OCI-compliant container images from container registries, similar to other container runtimes.

7. **Pod Support**: CRI-O supports Kubernetes pods, which are the smallest deployable units in Kubernetes. Pods can consist of one or more containers that share the same network namespace, storage, and other resources. CRI-O manages these pod configurations efficiently.

In summary, CRI-O is a container runtime specifically tailored for Kubernetes environments. It focuses on providing a lightweight, secure, and Kubernetes-compatible runtime, making it a popular choice for organizations looking to use Kubernetes in a containerized environment. It offers a balance between the simplicity of containerd and the Kubernetes-specific features required for a production-grade Kubernetes cluster.

## CLI Utilities
CRI-O and Containerd are both container runtimes that are designed to work with Kubernetes via the Container Runtime Interface (CRI). `crictl` and `nrdctl` are command-line utilities associated with these container runtimes, each serving specific purposes in container management.

In summary, `crictl` is a versatile command-line utility for managing containers and pods when using CRI-compatible runtimes like CRI-O and Containerd in Kubernetes. On the other hand, `nrdctl` is an alternative container runtime that provides Docker compatibility while using Containerd as the backend. The choice between them depends on your specific needs and familiarity with Docker-like interfaces.

### `crictl`
`crictl` is a command-line interface (CLI) tool for interacting with container runtimes that implement the CRI, including CRI-O and Containerd. It provides a user-friendly way to manage containers and pods on a Kubernetes cluster.

#### Features
   - **Container Management**: `crictl` allows you to create, run, stop, and delete containers.
   - **Image Management**: You can use `crictl` to pull, push, and list container images.
   - **Pod Management**: It supports operations on pods, such as creating and listing pods.
   - **Runtime Information**: You can retrieve runtime-specific information like container runtime status and version.
   - **Logs and Attach**: It provides options to view container logs and attach to a running container for troubleshooting.

#### Use Cases
Kubernetes administrators and developers often use `crictl` for debugging and troubleshooting container-related issues on Kubernetes nodes. It's especially helpful when you need to inspect containers and pods running on the cluster directly from the node.

#### Examples
```bash
# List all containers
crictl ps

# List all containers, including those in all namespaces
crictl ps --all

# List all images
crictl images

# Pull an image
crictl pull <image_name>

# Create a new container (example uses busybox)
crictl runp --name my-container <image_name>

# List the logs for a container
crictl logs <container_id>

# Get container info
crictl inspect <container_id>

# Exec into a running container
crictl exec -it <container_id> /bin/sh

# Stop a container
crictl stop <container_id>

# Remove a container
crictl rm <container_id>
```

### `nrdctl`
`nrdctl`, which stands for "Nerdctl," is not directly related to CRI-O or Containerd but is rather an alternative container runtime that aims to be a drop-in replacement for Docker. It provides a more Docker-compatible interface while still using Containerd under the hood.

#### Features
   - **Docker Compatibility**: `nrdctl` aims to be compatible with Docker commands and syntax, making it easier for users transitioning from Docker to Kubernetes.
   - **Containerd Backend**: Underneath, `nrdctl` uses Containerd as the container runtime, providing a streamlined and compatible experience for Docker users.
   - **OCI Compatibility**: It supports Open Container Initiative (OCI) standards for container images and runtimes.

#### Use Cases
`nrdctl` is often used by users and organizations that are familiar with Docker and want to maintain a Docker-like experience when working with containers in Kubernetes, even when using Containerd as the runtime.

#### Examples
```bash
# List available resources
nrdctl list

# Show detailed information about resources
nrdctl describe <resource_name>

# Monitor CPU resources in real-time
nrdctl monitor cpu

# Monitor memory resources in real-time
nrdctl monitor memory

# Monitor network resources in real-time
nrdctl monitor network

# Display resource allocation limits for a container (example uses containerd)
nrdctl container limits <container_id>

# Set resource limits for a container (example sets CPU limit)
nrdctl container set <container_id> cpu=200m

# Remove resource limits for a container (example removes CPU limit)
nrdctl container remove <container_id> cpu

# Pin a container to specific CPUs (example pins to CPUs 0 and 1)
nrdctl container pin <container_id> cpu 0,1

# Unpin a container from specific CPUs (example unpins from CPUs 0 and 1)
nrdctl container unpin <container_id> cpu 0,1
```
