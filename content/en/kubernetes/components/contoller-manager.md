---
title: "Contoller Manager"
description: ""
date: 2023-08-25T16:55:42Z
lastmod: 2023-08-25T16:55:42Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "components"
weight: 1400
toc: true
---
the `kube-controller-manager` is a critical component of Kubernetes that maintains the desired state of your cluster by continuously monitoring and managing various aspects of the cluster, such as node health, pod replication, and service discovery. It helps ensure that the desired state specified in your cluster configuration is continually maintained.

## Example

### Node Controller

The Node Controller is responsible for monitoring the health of nodes within the cluster and taking corrective actions when a node becomes unhealthy or unreachable. To accomplish this, it uses several parameters and configurations, including:

1. **Monitor Period**: The monitor period, often referred to as the `node-monitor-period`, is a configuration parameter that specifies how frequently the Node Controller should check the health of nodes in the cluster. It is typically set in seconds. During each monitor period, the Node Controller will perform a health check on all the nodes in the cluster to determine if they are responsive.

2. **Node Monitor Grace Period**: The node monitor grace period, sometimes called the `node-monitor-grace-period`, is another configuration parameter. It represents the amount of time, in seconds, that a node is given to respond to the health check before it is considered unhealthy. If a node doesn't respond within this grace period, it is marked as unhealthy, and the Node Controller may take action, such as initiating a pod eviction.

3. **Pod Eviction Timeout**: The pod eviction timeout, also known as the `pod-eviction-timeout`, is a configuration parameter that specifies the amount of time the Node Controller should wait for pods on an unhealthy node to gracefully terminate (if they can be terminated gracefully) before forcibly terminating them. This timeout is typically set in seconds. If the pods do not terminate within this period, the Node Controller will forcefully terminate them to ensure that they do not consume resources on an unhealthy node.

### Replication Controller and ReplicaSet Controller

These controllers ensure that the specified number of pod replicas are running and healthy. They create or delete pods as necessary to maintain the desired replica count.

### Deployment Controller

Manages the lifecycle of deployments, ensuring that the desired number of replicas are running, managing rolling updates, and performing rollbacks when needed.

### StatefulSet Controller

Responsible for managing StatefulSets, which are used for deploying stateful applications. It ensures that pods are created and maintained in a specific order, and each pod has a stable network identity.

### Service Account and Token Controller

Manages the lifecycle of service accounts and tokens used for authentication within the cluster.

### Endpoint Controller

Updates the Endpoints resource to reflect changes in Service objects, ensuring that traffic is directed to the correct pods.

### Namespace Controller

Ensures that namespaces are created and deleted as specified, and it helps maintain the integrity of namespace resources.

### Service Controller

Monitors and maintains the state of Service objects, ensuring that they correctly map to the pods they are supposed to expose.

### Job Controller and CronJob Controller

These controllers manage batch processing jobs. The Job Controller ensures that jobs complete successfully, while the CronJob Controller schedules and manages jobs at specified intervals.

### DaemonSet Controller

Ensures that DaemonSets have the desired pods running on each node in the cluster.

### Endpointslice Controller

Manages EndpointSlice resources, which provide more efficient and scalable service endpoint discovery.

### Pod GC (Garbage Collection) Controller

Helps clean up orphaned pods that no longer have a parent resource.

Each of these controllers operates independently and watches the Kubernetes API server for changes to relevant resources. When a resource's state deviates from the desired state, the controller takes corrective actions to reconcile the state.

## Installation

### Manual Installation

Manually installing the Kubernetes Controller Manager (`kube-controller-manager`) is not a common practice because Kubernetes components are typically installed and managed through cluster management tools like kubeadm, kops, or managed Kubernetes services provided by cloud providers. However, if you have a specific reason for manually installing it or need to do so for educational purposes, you can follow these general steps.

Please note that these instructions assume you have a basic understanding of Kubernetes and have already set up a working Kubernetes cluster. Also, these steps may vary slightly depending on your Kubernetes version and operating system.

Here are the general steps to manually install `kube-controller-manager`:

1. **Prerequisites**:
   - A running Kubernetes cluster with a working `kube-apiserver`.
   - Administrative access to the cluster.
   - SSH access to the machine where you want to install the Controller Manager.

2. **Download the Kubernetes Binary**:
   You can download the Kubernetes binary for the Controller Manager from the official Kubernetes GitHub repository: https://github.com/kubernetes/kubernetes/releases

   Ensure that you download the binary corresponding to your Kubernetes version.

3. **Copy the Binary**:
   Transfer the downloaded `kube-controller-manager` binary to the machine where you want to install it. You can use tools like `scp` for this purpose.

4. **Create a systemd Service Unit**:
   You will typically run `kube-controller-manager` as a systemd service. Create a systemd service unit file (e.g., `/etc/systemd/system/kube-controller-manager.service`) and define the service:

   ```systemd
   [Unit]
   Description=Kubernetes Controller Manager
   Documentation=https://github.com/kubernetes/kubernetes

   [Service]
   ExecStart=/path/to/kube-controller-manager \
     --address=0.0.0.0 \                # Controller Manager API server address
     --port=10252 \                     # Controller Manager API server port
     --master=https://api-server-ip:6443 \ # URL to the Kubernetes API server
     --allocate-node-cidrs=true \       # Allocate CIDRs to nodes for Pods
     --cluster-cidr=10.244.0.0/16 \     # Cluster CIDR
     --node-cidr-mask-size=24 \         # Node CIDR mask size
     --kubeconfig=/etc/kubernetes/controller-manager.conf \ # Controller Manager configuration file
     --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf \ # Authentication configuration file
     --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf \  # Authorization configuration file
     --service-account-private-key-file=/etc/kubernetes/pki/sa.key \       # Service Account key file
     --root-ca-file=/etc/kubernetes/pki/ca.crt \                          # CA certificate file
     --leader-elect=true                # Enable leader election
   Restart=on-failure
   RestartSec=5

   [Install]
   WantedBy=multi-user.target
   ```

   Be sure to replace placeholders like `/path/to/kube-controller-manager`, `https://api-server-ip:6443`, and file paths with appropriate values for your cluster.

5. **Start and Enable the Service**:
   Once you've created the systemd unit file, start and enable the `kube-controller-manager` service:

   ```bash
   systemctl start kube-controller-manager
   systemctl enable kube-controller-manager
   ```

6. **Verify Installation**:
   You can check the status of the Controller Manager service to ensure it's running without errors:

   ```bash
   systemctl status kube-controller-manager
   ```

Remember that this is a simplified overview of the process. In a production environment, you would also consider configuring RBAC (Role-Based Access Control), setting up the Controller Manager's configuration file, and managing TLS certificates securely. Additionally, it's highly recommended to use Kubernetes deployment tools or infrastructure automation to simplify the installation and management of Kubernetes components.

## `kubeadm` Installation

1. **Prerequisites:**
   - Make sure `kubeadm` is installed on your control plane node.

2. **Initialize Cluster (if not already done):**
   - If you haven't already initialized your Kubernetes cluster with `kubeadm`, you can do so using the following command:

     ```bash
     kubeadm init --apiserver-advertise-address=YOUR_CONTROL_PLANE_IP
     ```

3. **Install kube-controller-manager:**
   - `kubeadm` can be used to install and configure the `kube-controller-manager`. Run the following command, specifying the desired flags and options:

     ```bash
     sudo kubeadm init phase control-plane controller-manager --config=/path/to/kubeadm-config.yaml
     ```

   - In the `kubeadm-config.yaml` file, you can specify various options for the controller manager, such as API server endpoints, certificates, and feature gates.

4. **Start kube-controller-manager:**
   - If it's not already running, you can start the `kube-controller-manager` process using a systemd unit file provided by `kubeadm`. For example:

     ```bash
     sudo systemctl start kube-controller-manager
     ```

   - Enable it to start on boot if necessary:

     ```bash
     sudo systemctl enable kube-controller-manager
     ```

## Commands

```bash
# View kube-controller-manager options (manual installation)
cat /etc/systemd/system/kube-controller-manager.service

# View kube-controller-manager options (kubeadm installation)
cat /etc/kubernetes/manifests/kube-controller-manager.yaml

# View kube-contoller-manager options by listing the process on the master node and searching for kube-contoller-manager
ps -aux | grep kube-contoller-manager
```
