---
title: "Kube Scheduler"
description: ""
date: 2023-08-25T17:36:39Z
lastmod: 2023-08-25T17:36:39Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "components"
weight: 1400
toc: true
---

`kube-scheduler` is a critical component of Kubernetes that ensures the optimal placement of pods on nodes within a cluster, considering various constraints and factors to maintain application performance, resource utilization, and high availability. It plays a pivotal role in automating workload distribution in a Kubernetes environment.

Here's how `kube-scheduler` works and what its responsibilities are:

1. **Pod Scheduling Request**: When a user or system administrator creates a pod deployment, replication controller, or other workload with a desired number of replicas, Kubernetes sends a pod scheduling request to the `kube-scheduler`.

2. **Node Selection**: The `kube-scheduler` evaluates various factors and constraints to select the most suitable node for the pod. These factors and constraints include:

   - **Resource Requirements**: It considers the CPU and memory resource requests and limits defined in the pod's configuration to ensure that the node has enough resources available.

   - **Node Affinity/Anti-Affinity**: Node affinity rules allow you to specify conditions that must be met for a pod to be scheduled on a particular node. Node anti-affinity rules prevent pods from being scheduled on nodes that already have pods that match certain criteria.

   - **Pod Affinity/Anti-Affinity**: Similar to node affinity, these rules define how pods should be placed in relation to other pods.

   - **Taints and Tolerations**: Nodes can be "tainted" to repel certain pods unless those pods have specific "tolerations" that allow them to run on tainted nodes.

   - **Resource Constraints**: The scheduler considers node-specific resource constraints, such as the amount of available CPU and memory on each node.

   - **Pod Priority**: Pods can be assigned priorities, which influence their scheduling order. Higher-priority pods are scheduled before lower-priority pods.

   - **Inter-Pod Spreading**: To enhance application availability, the scheduler can be configured to spread pods across different nodes to avoid single points of failure.

3. **Node Scoring**: The scheduler assigns a score to each node based on the criteria mentioned above. Nodes with higher scores are better candidates for the pod.

4. **Node Selection**: The scheduler selects the node with the highest score as the destination for the pod.

5. **Binding**: Once the scheduler has selected a node, it updates the Kubernetes API server with the binding information, indicating which node the pod should be scheduled on.

6. **Pod Placement**: The Kubernetes control plane, including the kubelet on the selected node, then takes care of placing the pod's containers onto the chosen node.

7. **Monitoring**: The `kube-scheduler` continually monitors the cluster's state and reschedules pods if necessary. For example, if a node goes offline or becomes unsuitable due to resource constraints, the scheduler can relocate pods to other available nodes.

## Commands

```bash
# VView kube-scheduler options (kubeadm installation)
cat /etc/kubernetes/manifests/kube-scheduler.yaml

# View api-server options by listing the process on the master node and searching for kube-apiserver
ps -aux | grep kube-scheduler
```
