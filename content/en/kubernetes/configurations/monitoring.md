---
title: "Monitoring"
description: ""
date: 2023-07-26T07:37:22+02:00
lastmod: 2023-07-26T07:37:22+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1080
toc: true
---

## CPU and Memory Usage
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
kubectl top pod <pod-name>
```

To get the CPU and memory usage of containers within a pod:

```shell
kubectl top pod <pod-name> --containers
```
These commands will provide you with real-time metrics data from the Metric Server, helping you monitor and troubleshoot your Kubernetes cluster.


## Metric Server
Kubernetes Metric Server is a component of the Kubernetes monitoring stack that collects resource utilization data from the nodes and containers in a Kubernetes cluster. It provides real-time metrics such as CPU and memory usage, network traffic, and file system usage. These metrics are essential for monitoring the health and performance of your cluster and its workloads.

To activate and access the Kubernetes Metric Server, you need to follow these steps:

1. Check if Metric Server is already installed: Run the following command to check if Metric Server is already deployed in your cluster:
```shell
kubectl get deployment -n kube-system metrics-server
```

2. Install Metric Server: If Metric Server is not installed, you can deploy it using the following command:
```shell
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

3. Verify the installation: Wait for a few moments until the Metric Server components are deployed. You can check the deployment status using the following command:
```shell
kubectl get deployment -n kube-system metrics-server
```

4. Access the metrics: Once Metric Server is running, you can access the metrics using the kubectl top command. Here are a few examples:
