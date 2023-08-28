---
title: "Metric Server"
description: ""
date: 2023-08-28T06:01:06Z
lastmod: 2023-08-28T06:01:06Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "monitoring"
weight: 1102
toc: true
---

## Requirements

Metrics Server imposes specific cluster and network configuration prerequisites that might not be the default settings for all cluster distributions. To ensure compatibility with Metrics Server, please verify that your cluster distribution meets these requirements:

* The kube-apiserver must have the aggregation layer enabled.

* Webhook authentication and authorization should be activated on all nodes.

* Kubelet certificates must be signed by the cluster's Certificate Authority. Alternatively, you can bypass certificate validation by using the `--kubelet-insecure-tls` flag when configuring Metrics Server.

* The container runtime must either support container metrics RPCs or have cAdvisor support.

* The network should facilitate the following communication:

   - Allow the control plane to connect to Metrics Server. The control plane node must be able to reach Metrics Server's pod IP and port 10250 (or node IP and a custom port if hostNetwork is enabled). You can find more details about control plane to node communication in the provided link.

   - Metrics Server should be able to connect to the Kubelet on all nodes. Metrics Server needs to access the node's address and the Kubelet port. These addresses and ports are configured in Kubelet and are published as part of the Node object. Addresses are found in the `.status.addresses` field, and the port is specified in the `.status.daemonEndpoints.kubeletEndpoint.port` field (default is 10250). Metrics Server will select the first node address based on the list provided by the `kubelet-preferred-address-types` command line flag (default is InternalIP, ExternalIP, Hostname in manifests).


## Installation
Metrics Server can be installed either directly from YAML manifest or via the official Helm chart. 

### Helm

#### Add the Metrics Server Helm Repository

```bash
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
```

#### Install Metrics Server

```bash
helm upgrade --install metrics-server metrics-server/metrics-server
```

This command installs the Metrics Server using the "stable/metrics-server" chart from the Helm repository. You can customize the installation by providing your own configuration values using the `-f` or `--set` flags.

For example, to customize the installation with specific resource requests and limits, create a YAML file (e.g., `metrics-server-values.yaml`) with your configuration:

```yaml
replicaCount: 2
resources:
    requests:
    memory: "64Mi"
    cpu: "250m"
    limits:
    memory: "128Mi"
    cpu: "500m"
```

Then, install Metrics Server with your custom values file:

```bash
helm install metrics-server metrics-server/metrics-server -f metrics-server-values.yaml
```

#### Verify Installation

After a successful installation, you can verify that Metrics Server is running by checking the pods in your cluster:

```bash
kubectl get pods -n kube-system | grep metrics-server
```

You should see Metrics Server pods listed in the "kube-system" namespace.

### Manual

To install the latest Metrics Server release from the components.yaml manifest, run the following command:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

*Note:* To install it on Docker Desktop Kubernetes, add the line `--kubelet-insecure-tls` under the container args section 


## Commands
CPU and Memory Usage

### Nodes
To get the CPU and memory usage of nodes:
```shell
$ kubectl top nodes
NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
controlplane   271m         0%     1184Mi          0%        
node01         228m         0%     298Mi           0%    
```

### Pods
To get the CPU and memory usage of pods:
```shell
$ kubectl top pods
NAME       CPU(cores)   MEMORY(bytes)   
elephant   14m          32Mi            
lion       1m           18Mi            
rabbit     102m         252Mi 
```

To get the CPU and memory usage of a specific pod:
```shell
$ kubectl top pod <pod-name>
```

To get the CPU and memory usage of containers within a pod:

```shell
$ kubectl top pod <pod-name> --containers
```
