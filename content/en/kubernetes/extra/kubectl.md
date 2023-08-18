---
title: "Kubectl Cheatsheet"
description: ""
lead: ""
date: 2023-07-16T09:29:02+02:00
lastmod: 2023-07-16T09:29:02+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "extra"
weight: 100
toc: true
---
## Cluster Information
```shell
kubectl version                        # Display the Kubernetes client and server versions
kubectl cluster-info                   # Display cluster information
kubectl config view                    # View and edit kubeconfig
```

## Basic Operations
```shell
kubectl get all                        # List all resources of all types in the current namespace
kubectl get pods                       # List all pods in the current namespace
kubectl get nodes                      # List all nodes in the cluster
kubectl get namespaces                 # List all namespaces
kubectl get deployments                # List all deployments in the current namespace
kubectl get services                   # List all services in the current namespace
kubectl get configmaps                 # List all config maps in the current namespace
kubectl get secrets                    # List all secrets in the current namespace
kubectl get events                     # List all events in the current namespace
```

## Detailed Information
```shell
kubectl describe pod <pod_name>        # Show detailed information about a specific pod
kubectl describe node <node_name>      # Show detailed information about a specific node
kubectl describe service <svc_name>    # Show detailed information about a specific service
```

## Create and Apply
```shell
kubectl create -f <file.yaml>          # Create a resource from a YAML file
kubectl apply -f <file.yaml>           # Apply changes to a resource from a YAML file
kubectl apply -f <url>                 # Apply a resource from a URL
```

## Editing Resources
```shell
kubectl edit <resource_type> <resource_name> # Edit a resource using the default editor
```

## Delete Resources
```shell
kubectl delete pod <pod_name>          # Delete a specific pod
kubectl delete deployment <dep_name>   # Delete a specific deployment
kubectl delete service <svc_name>      # Delete a specific service
```

## Labeling Resources
```shell
kubectl label <resource_type> <resource_name> key=value  # Add a label to a resource
```

## Scaling
```shell
kubectl scale deployment <dep_name> --replicas=<replica_count>  # Scale a deployment to a specific number of replicas
```

## Taints and Tolerations
```shell
kubectl taint nodes <node_name> key=value:taint-effect  # Add a taint to a node
kubectl taint nodes <node_name> key=value:NoSchedule    # Add a NoSchedule taint to a node
```

## Resource Quotas
```shell
kubectl get resourcequota                       # List resource quotas in the current namespace
kubectl describe resourcequota <quota_name>     # Describe a specific resource quota
```

## Networking
```shell
kubectl port-forward <pod_name> <local_port>:<pod_port>  # Forward local port to a specific pod's port
```

## Copying Files and Directories
```shell
kubectl cp /tmp/foo_dir my-pod:/tmp/bar_dir            # Copy /tmp/foo_dir local directory to /tmp/bar_dir in a remote pod in the current namespace
kubectl cp /tmp/foo my-pod:/tmp/bar -c my-container    # Copy /tmp/foo local file to /tmp/bar in a remote pod in a specific container
kubectl cp /tmp/foo my-namespace/my-pod:/tmp/bar       # Copy /tmp/foo local file to /tmp/bar in a remote pod in namespace my-namespace
kubectl cp my-namespace/my-pod:/tmp/foo /tmp/bar       # Copy /tmp/foo from a remote pod to /tmp/bar locally
```

## Logs and Troubleshooting
```shell
kubectl logs <pod_name>                # View the logs of a specific pod
kubectl logs -f <pod_name>             # Stream the logs of a specific pod
kubectl exec -it <pod_name> -- <command>  # Execute a command inside a specific pod
kubectl top node                       # Show CPU and memory usage for nodes
kubectl top pod                        # Show CPU and memory usage for pods
kubectl get events                     # List all events in the current namespace
```

## Formatting Output
To output details to your terminal window in a specific format, add the `-o` or `--output` flag to a supported kubectl command.


| Format                                   | Description                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------|
| `-o=custom-columns=<spec>`               | Print a table using a comma separated list of custom columns                                             |
| `-o=custom-columns-file=<filename>`      | Print a table using the custom columns template in the <filename> file                             |
| `-o=json`                                | Output a JSON formatted API object                                                                       |
| `-o=jsonpath=<template>`                 | Print the fields defined in a jsonpath expression                                                        |
| `-o=jsonpath-file=<filename>`            | Print the fields defined by the jsonpath expression in the &lt;filename&gt; file                         |
| `-o=name`                                | Print only the resource name and nothing else                                                            |
| `-o=wide`                                | Output in the plain-text format with any additional information, and for pods, the node name is included |
| `-o=yaml`                                | Output a YAML formatted API object                                                                       |
