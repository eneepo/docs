---
title: "Kubelet"
description: ""
date: 2023-08-25T18:08:40Z
lastmod: 2023-08-25T18:08:40Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "components"
weight: 1220
toc: true
---

Kubelet runs on each node in the cluster and is responsible for managing the containers that are scheduled to run on that node. Kubelet acts as an intermediary between the Kubernetes control plane (e.g., the API server) and the container runtime (e.g., Docker, containerd) on the node. 

*Note: kubelet is not installed with kubeadm and must be installed manually on each node*

## Responsibilities

1. **Pod Lifecycle Management:**
   - Kubelet is responsible for ensuring that the containers defined within pods are running as expected on its node.
   - It communicates with the container runtime to start, stop, and manage container processes.
   - It monitors the health of containers and restarts them if they fail or crash, based on the pod's restart policy.

2. **Node Registration:**
   - Kubelet registers the node with the Kubernetes control plane, providing essential information about the node's capacity, hostname, and available resources.
   - It reports the node's status and conditions (e.g., ready or not ready) to the control plane, allowing the scheduler to make informed decisions about pod placement.

3. **Container Image Management:**
   - Kubelet ensures that the required container images are pulled from a container registry and available locally on the node before starting pods.
   - It periodically checks for updated images and pulls them if necessary, based on the image pull policy defined in the pod specifications.

4. **Volume Management:**
   - Kubelet manages and mounts volumes specified in pod configurations, ensuring that the required storage is available to containers.
   - It integrates with the container runtime to create, attach, and detach volumes as needed.

5. **Security and Isolation:**
   - Kubelet enforces container runtime security settings, such as AppArmor or SELinux profiles, to ensure proper container isolation.
   - It may also perform runtime-specific security tasks, such as setting up seccomp profiles.

6. **Resource Management:**
   - Kubelet monitors the resource usage of containers and enforces resource constraints (CPU and memory limits) defined in pod specifications.
   - It reports resource usage and capacity information back to the control plane, which is used for scheduling decisions.

7. **CAdvisor Integration:**
   - Kubelet integrates with Container Advisor (cAdvisor), a component that provides detailed resource usage and performance metrics for containers.
   - These metrics are exposed through the Kubernetes API and can be used for monitoring and scaling purposes.

8. **Pod Cleanup:**
   - Kubelet is responsible for cleaning up pods and associated resources when they are no longer needed or have been deleted.

9. **Event Reporting:**
   - Kubelet reports various events and status updates to the Kubernetes API server, allowing administrators to monitor the health and state of nodes and pods.

In summary, kubelet plays a crucial role in the Kubernetes cluster by ensuring that containers within pods are correctly managed on each node. It interacts with the control plane, container runtime, and other components to maintain the desired state of the pods, manage resources, and provide essential node information to the Kubernetes control plane.

## Installation

To manually install kubelet on a node and create a service definition, you can follow these general steps. Please note that the specific commands and file paths may vary depending on your Linux distribution and Kubernetes version. Below, I'll provide a general guide that should work for many Linux distributions.

**Step 1: Install Dependencies**

Before installing kubelet, make sure you have the necessary dependencies installed, including `cni-plugins` and `containerd` (or your preferred container runtime).

**Step 2: Download Kubelet**

Use `wget` or another download method to obtain the kubelet binary for your Kubernetes version from the official Kubernetes GitHub releases page. Replace `<KUBE_VERSION>` with the desired version:

```bash
KUBE_VERSION=<KUBE_VERSION>
wget https://dl.k8s.io/release/${KUBE_VERSION}/bin/linux/amd64/kubelet
```

Make the kubelet binary executable:

```bash
chmod +x kubelet
```

**Step 3: Create kubelet Configuration File**

Create a kubelet configuration file (e.g., `/etc/kubernetes/kubelet.yaml`) with the appropriate configuration options. Below is a minimal example:

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
hostnameOverride: <NODE_NAME>
```

Replace `<NODE_NAME>` with the actual hostname of your node.

**Step 4: Create a systemd Service Definition**

Create a systemd service definition file (e.g., `/etc/systemd/system/kubelet.service`) for kubelet. Here's an example:

```ini
[Unit]
Description=Kubernetes Kubelet
Documentation=https://kubernetes.io/docs/home/

[Service]
ExecStart=/path/to/kubelet \
  --config=/etc/kubernetes/kubelet.yaml \
  --kubeconfig=/etc/kubernetes/kubeconfig.yaml \
  --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubeconfig.yaml \
  --v=2
Restart=always
KillMode=process
RestartSec=10
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
```

In the `ExecStart` line, replace `/path/to/kubelet` with the actual path to the kubelet binary.

**Step 5: Reload and Start kubelet**

```bash
# Reload the systemd daemon to recognize the new service:
sudo systemctl daemon-reload

# Enable kubelet to start at boot:
sudo systemctl enable kubelet

# Start the kubelet service:
sudo systemctl start kubelet
```

**Step 6: Verify kubelet Status**

Check the status of kubelet to ensure it's running without errors:

```bash
sudo systemctl status kubelet
```

**Step 7: Optional - Add Node to Cluster**

If this node is not already part of a Kubernetes cluster, you'll need to add it. This typically involves approving a Certificate Signing Request (CSR) on the cluster's master node. The exact process depends on your cluster setup and policies.

Remember to configure appropriate networking (e.g., CNI plugins) and container runtime (e.g., Docker, containerd) on the node to ensure proper container orchestration.


## Commands

```bash
# View api-server options by listing the process on the master node and searching for kube-apiserver
ps -aux | grep kubelet
```
