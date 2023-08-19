---
title: "Components"
description: ""
date: 2023-08-19T14:14:36Z
lastmod: 2023-08-19T14:14:36Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "extra"
weight: 1100
toc: true
---
Kubernetes is comprised of various components that work together to provide its core functionality. These components can be categorized into several groups:

1. **Control Plane Components**:

   - **kube-apiserver**: This is the central management component that exposes the Kubernetes API. It serves as the entry point for all administrative tasks, including deploying, configuring, and managing applications.

   - **etcd**: A distributed key-value store used for storing all the cluster's configuration data. It acts as the cluster's "source of truth" and provides a consistent and highly available storage solution.

   - **kube-scheduler**: This component is responsible for determining which worker nodes should run the pods (containers) based on resource requirements, policies, and constraints.

   - **kube-controller-manager**: It runs various controller processes that regulate the state of the system. Examples include the Node Controller (ensures nodes are healthy), the Replication Controller (maintains the desired number of replicas), and the Service Controller (manages service endpoints).

   - **cloud-controller-manager**: An optional component that interacts with the underlying cloud infrastructure (e.g., AWS, GCP, Azure) to manage resources like Load Balancers and Persistent Volumes.

2. **Node Components**:

   - **kubelet**: The primary agent running on each node in the cluster. It communicates with the control plane and ensures that containers are running in a pod. It also performs various health checks.

   - **kube-proxy**: Maintains network rules on nodes. It manages network routing for services in order to ensure that network traffic is properly load-balanced to the appropriate pods.

   - **Container Runtime**: The software responsible for running containers, such as Docker, containerd, or CRI-O. Kubernetes supports various container runtimes.

3. **Add-ons**:

   - **DNS**: A DNS server that provides DNS-based service discovery for services in the cluster.

   - **Dashboard**: A web-based user interface for managing and monitoring the cluster.

   - **Ingress Controller**: Manages external access to services within the cluster, typically through HTTP or HTTPS.

   - **Heapster (deprecated)**: Used for cluster-wide monitoring and performance analysis. In recent versions, this has been replaced by the Metrics Server and external monitoring solutions.

4. **Networking Plugins**: Kubernetes supports various networking plugins that enable communication between pods across nodes. Examples include Calico, Flannel, and Weave.

5. **Storage Plugins**: Kubernetes can manage storage volumes and supports different storage solutions like AWS EBS, GCE PD, and network-attached storage.

6. **Authentication and Authorization**: These are not standalone components but are integral to Kubernetes. They control who can access the cluster and what actions they can perform. Kubernetes can integrate with various identity providers and access control mechanisms.

These components work together to provide the core features and functionality of Kubernetes, allowing you to manage and scale containerized applications efficiently and reliably. Note that the specific components and their configurations can vary depending on the Kubernetes distribution and the environment in which it is deployed.