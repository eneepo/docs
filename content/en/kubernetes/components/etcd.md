---
title: "ETCD"
description: ""
date: 2023-08-22T07:11:51Z
lastmod: 2023-08-22T07:11:51Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "components"
weight: 1200
toc: true
---
`etcd` is a distributed key-value store that is widely used as the primary data store for Kubernetes. It plays a critical role in maintaining the configuration and state information of a Kubernetes cluster. etcd is responsible for storing and synchronizing data, including cluster configuration, metadata, and the state of all resources managed by Kubernetes.

Here are some key points about etcd in Kubernetes:

1. **Consistency and Reliability**: etcd is designed to provide strong consistency and high availability. It uses the Raft consensus algorithm to ensure data consistency across a distributed cluster of etcd nodes.

2. **Data Model**: etcd stores data in a simple key-value format. Kubernetes resources and their configurations are represented as keys and values within etcd, making it a central source of truth for the entire cluster.

3. **Configuration and State**: Kubernetes cluster configuration, such as API server settings, authorization policies, and network configurations, is stored in etcd. Additionally, the current state of all Kubernetes resources (pods, services, nodes, etc.) is maintained in etcd.

4. **Watch Mechanism**: Kubernetes components, like the API server and controllers, watch specific keys in etcd to detect changes. This watch mechanism enables real-time reactions to changes in the cluster's state.

5. **Backup and Disaster Recovery**: etcd data is critical for the stability and operation of a Kubernetes cluster. Therefore, it's essential to regularly back up etcd data and have disaster recovery plans in place.

## Installation

Depending on how you set up your cluster, etcd is deployed differently. There are two common ways to install etcd: manually and using `kubeadm`, which is a popular tool for setting up Kubernetes clusters. 

### Manual Installation

1. **Download etcd**:
   - Visit the [etcd GitHub releases page](https://github.com/etcd-io/etcd/releases) to find the latest release.
   - Download the appropriate etcd binary for your operating system. Commonly used distributions, like Linux and macOS, are supported.

2. **Install etcd**:
   - Extract the downloaded binary if it's in an archive format (e.g., tar.gz).
   - Place the `etcd` binary in a directory that's in your system's PATH so you can run it from anywhere.

3. **Configure etcd**:
   - Create a configuration file for etcd. A simple configuration might look like this:

   ```yaml
   name: my-etcd
   data-dir: /var/lib/etcd
   listen-client-urls: http://localhost:2379
   advertise-client-urls: http://localhost:2379
   initial-advertise-peer-urls: http://localhost:2380
   listen-peer-urls: http://localhost:2380
   initial-cluster-token: my-etcd-token
   initial-cluster: my-etcd=http://localhost:2380
   initial-cluster-state: new
   ```

   - Adjust the configuration according to your needs and save it to a file (e.g., `/etc/etcd/etcd.conf`).

4. **Start etcd**:
   - Run etcd using the configuration file you created:

   ```bash
   etcd --config-file /etc/etcd/etcd.conf
   ```

   - Ensure etcd is running and listening on the specified endpoints.

### Installation with `kubeadm`

1. **Install `kubeadm` and Prerequisites**:
   - Install `kubeadm`, `kubelet`, and `kubectl` on your host machine. These tools are part of the Kubernetes distribution and are typically used to set up and manage Kubernetes clusters.

2. **Initialize the Control Plane Node**:
   - Use `kubeadm` to initialize the control plane node of your Kubernetes cluster. During initialization, `kubeadm` will automatically install and configure etcd for you.

   ```bash
   kubeadm init --control-plane-endpoint=<control-plane-ip>
   ```

   - Follow the instructions provided by `kubeadm` to set up your Kubernetes control plane. This includes running commands to install network plugins, joining worker nodes, and configuring `kubectl`.

3. **Verify etcd Installation**:
   - After initializing the control plane, you can use `kubectl` to verify that etcd is running as part of your Kubernetes cluster. You should see etcd pods in the `kube-system` namespace by running:

   ```bash
   kubectl get pods -n kube-system | grep etcd
   ```

   - And to list all keys stored by Kubernetes, run:

   ```bash
   kubectl exec etcd-master -n kube-system -- sh -c "ETCDCTL_API=3 etcdctl get / --prefix --keys-only --limit=10 
      --cacert /etc/kubernetes/pki/etcd/ca.crt 
      --cert /etc/kubernetes/pki/etcd/server.crt
      --key /etc/kubernetes/pki/etcd/server.key" 
   ```

   - You must also specify path to certificate files so that ETCDCTL can authenticate to the ETCD API Server.

Using `kubeadm` simplifies the installation of etcd within a Kubernetes cluster, as it takes care of many of the configuration details. This method is recommended for setting up Kubernetes clusters unless you have specific requirements that necessitate a manual installation of etcd.

## `etcdctl`

`etcdctl` is a command-line client tool for interacting with etcd clusters. It allows you to perform various operations on the etcd key-value store, such as setting keys, getting values, watching for changes, and more.

### Commands

For the complete list view [etcdctl documentation](https://etcd.io/docs/v3.4/dev-guide/interacting_v3/).

```bash
# Set a key-value pair in etcd
etcdctl put <key> <value>

# Get the value associated with a key
etcdctl get <key>

# Get all keys with a specific prefix
etcdctl get --prefix <prefix>

# Delete a key
etcdctl del <key>

# Watch for changes on a specific key
etcdctl watch <key>

# List all keys and values in etcd
etcdctl ls --recursive

# Create a new directory
etcdctl mkdir <directory>

# List all directories
etcdctl rmdir <directory>

# View what API version etccdtl is configured to work with
etcdctl version

# Set API version 3
ETCDCTL_API=3 etcdctl version

# Enable v3 API mode (required for some commands)
etcdctl --endpoints=<etcd-endpoint> --api-version=3 <command>

# Backup etcd data
etcdctl snapshot save <snapshot-file>

# Restore etcd data from a snapshot
etcdctl snapshot restore <snapshot-file> --data-dir=<data-directory>

# List members of the etcd cluster
etcdctl member list
```

Please note that the actual usage of `etcdctl` may vary depending on your etcd cluster configuration and version. Be cautious when using `etcdctl` in a production environment, especially for modifying data, as it can impact the stability of your Kubernetes cluster. Always refer to the official etcd documentation and ensure you have the necessary permissions to perform these operations in your cluster.