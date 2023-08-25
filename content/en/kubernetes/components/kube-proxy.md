---
title: "Kube Proxy"
description: ""
date: 2023-08-25T18:15:35Z
lastmod: 2023-08-25T18:15:35Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "components"
weight: 1225
toc: true
---

Kube-proxy is a network proxy service that runs on each node in a Kubernetes cluster. Its primary responsibility is to maintain network rules on nodes, allowing network communication to be properly directed to containers and pods. Kube-proxy plays a critical role in enabling networking features and ensuring that applications within the cluster can communicate with each other and with external resources. Here are its key responsibilities and an outline of how it works:

## Responsibilities

1. **Network Proxying**: Kube-proxy facilitates communication between various pods and services within the Kubernetes cluster. It sets up network rules (iptables rules on Linux or routing rules on Windows) to forward traffic from a service's virtual IP (VIP) to the actual pods backing that service.

2. **Load Balancing**: Kube-proxy performs simple load balancing for services that expose multiple pods. It evenly distributes incoming requests among the pods associated with a service to ensure efficient resource utilization and fault tolerance.

3. **Service Discovery**: Kube-proxy helps in service discovery by maintaining a mapping between a service's VIP and the corresponding pods' IP addresses. This allows applications within the cluster to communicate with each other using a stable, logical service name rather than dealing with individual pod IP addresses, which can change dynamically.

4. **Proxy Modes**:
   - **User-Space Proxying**: In this mode, Kube-proxy runs as a userspace proxy, handling network requests and forwarding them to the appropriate pods. This mode is less efficient but can work in environments with network restrictions.
   - **iptables Proxying**: This mode is the default for Kube-proxy on Linux nodes. It uses iptables rules to direct traffic to the appropriate pods, making it more efficient than the userspace mode.
   - **IPVS Proxying**: Kube-proxy can also be configured to use the IPVS (IP Virtual Server) mode, which is more efficient and scalable for large clusters, as it offloads load balancing to the Linux kernel.

5. **Endpoint Slices**: In recent versions of Kubernetes, Kube-proxy has been integrated with Endpoint Slices. Instead of configuring iptables rules for each individual pod, Kube-proxy now configures rules for endpoint slices, which are smaller, more manageable sets of endpoints. This improves scalability and reduces the complexity of iptables rules.

6. **Health Checking**: Kube-proxy performs periodic health checks on pods associated with a service. If a pod becomes unhealthy, Kube-proxy removes it from the load balancing pool, ensuring that traffic is directed only to healthy pods.

## How to Install kube-proxy

To install kube-proxy manually and create a systemd service definition, follow these general steps:

**Step 1: Download Kube-proxy Binary**

1. Use `wget` or another method to download the kube-proxy binary for your Kubernetes version from the official Kubernetes GitHub releases page. Replace `<KUBE_VERSION>` with your desired version.

   ```bash
   KUBE_VERSION=<KUBE_VERSION>
   wget https://dl.k8s.io/release/${KUBE_VERSION}/bin/linux/amd64/kube-proxy
   ```

2. Make the kube-proxy binary executable:

   ```bash
   chmod +x kube-proxy
   ```

**Step 2: Create a systemd Service Definition**

3. Create a systemd service definition file (e.g., `/etc/systemd/system/kube-proxy.service`) for kube-proxy. Here's an example for iptables mode:

   ```ini
   [Unit]
   Description=Kubernetes Kube-Proxy
   Documentation=https://kubernetes.io/docs/home/

   [Service]
   ExecStart=/path/to/kube-proxy \
     --kubeconfig=/etc/kubernetes/kubeconfig.yaml \
     --proxy-mode=iptables \
     --cluster-cidr=<CLUSTER_CIDR> \
     --v=2
   Restart=always
   KillMode=process
   RestartSec=10
   TimeoutStartSec=0

   [Install]
   WantedBy=multi-user.target
   ```

   Replace `/path/to/kube-proxy` with the actual path to the kube-proxy binary and `<CLUSTER_CIDR>` with your cluster's CIDR range.

**Step 3: Reload and Start kube-proxy**

4. Reload the systemd daemon to recognize the new service:

   ```bash
   sudo systemctl daemon-reload
   ```

5. Enable kube-proxy to start at boot:

   ```bash
   sudo systemctl enable kube-proxy
   ```

6. Start the kube-proxy service:

   ```bash
   sudo systemctl start kube-proxy
   ```

**Step 4: Verify kube-proxy Status**

7. Check the status of kube-proxy to ensure it's running without errors:

   ```bash
   sudo systemctl status kube-proxy
   ```
