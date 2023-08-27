---
title: "Limit Range"
description: ""
date: 2023-08-27T18:57:36Z
lastmod: 2023-08-27T18:57:36Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resource-management"
weight: 1212
toc: true
---

LimitRange helps administrators control and define resource limits and requests for pods in a namespace. It ensures that pods within a namespace adhere to specific resource constraints, preventing them from consuming more resources than allocated and potentially causing resource contention or cluster instability.

## Key Notes

1. **Resource Constraints**: LimitRange allows you to specify limits and requests for compute resources like CPU and memory. These limits define the maximum amount of resources a pod can consume, while requests set a minimum amount of resources guaranteed to the pod. This ensures fair resource distribution and avoids resource starvation in the cluster.

2. **Namespace Scope**: LimitRange is applied at the namespace level, which means you can define different resource constraints for each namespace. This is particularly useful in multi-tenant environments where different applications or teams share the same cluster but require distinct resource allocations.

3. **Resource Defaults**: You can set default values for resource limits and requests within a LimitRange. If a pod does not specify its resource requirements, Kubernetes assigns the default values defined in the LimitRange. This simplifies pod configuration and ensures that every pod has at least a minimum level of resources.

4. **Pod Quotas**: LimitRange complements the Kubernetes ResourceQuota feature. While ResourceQuotas restrict the total amount of resources a namespace can consume, LimitRange focuses on individual pod resource constraints. Together, these features provide comprehensive resource management capabilities for Kubernetes clusters.

5. **Validation and Enforcement**: When a pod is created or updated, the LimitRange is consulted to ensure that the defined resource limits and requests comply with the namespace's constraints. If the pod's resource requirements violate these constraints, Kubernetes will prevent the pod from starting, helping to maintain resource integrity.

6. **Resource Types**: In addition to CPU and memory, LimitRange can also be used to set constraints on other resources like GPUs and ephemeral storage, depending on your cluster's configuration and workload requirements.


## Example

```yaml
apiVersion: "v1"
kind: "LimitRange"
metadata:
  name: "example-limit-range"
spec:
  limits:
    - type: "Pod"
      max:
        cpu: "1"
        memory: "512Mi"
      min:
        cpu: "100m"
        memory: "64Mi"
    - type: "Container"
      default:
        cpu: "100m"
        memory: "64Mi"
      max:
        cpu: "200m"
        memory: "128Mi"
      min:
        cpu: "50m"
        memory: "32Mi"
```

In this example, the LimitRange defines resource constraints for both the entire pod and individual containers within the pod. It sets default and maximum values for CPU and memory, ensuring that pods and containers in the associated namespace conform to these limits.

LimitRange is a powerful tool for resource management in Kubernetes, providing fine-grained control over resource allocation, enhancing cluster stability, and preventing resource hogging by pods. It plays a vital role in maintaining the overall health and performance of Kubernetes clusters, especially in multi-tenant environments.