---
title: "Resource Quota"
description: ""
date: 2023-07-26T07:46:52+02:00
lastmod: 2023-07-26T07:46:52+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resource-management"
weight: 1210
toc: true
---

In Kubernetes, a ResourceQuota is a resource management feature that allows you to specify and enforce limits on the amount of compute resources (CPU and memory) and the number of objects (such as Pods, Services, ConfigMaps, and more) that can be used by a particular namespace within a cluster. ResourceQuotas are used to prevent resource overconsumption and ensure that different workloads running in different namespaces do not interfere with each other's resource requirements.

## Key points
1. **Namespace Scope**: ResourceQuotas are defined on a per-namespace basis. This means you can have different ResourceQuotas for different namespaces, depending on your cluster's requirements and organization.

2. **Resource Limits**: You can set limits on CPU and memory usage for Pods within a namespace. These limits ensure that a Pod cannot consume more resources than specified in the ResourceQuota.

3. **Object Count Limits**: ResourceQuotas can also limit the number of certain types of objects (e.g., Pods, Services, ConfigMaps, PersistentVolumeClaims) that can be created in a namespace. This is helpful for controlling the proliferation of resources.

4. **Hard and Soft Limits**: ResourceQuotas support both hard and soft limits. Hard limits are strict and will prevent resource consumption beyond the defined quota, while soft limits provide a grace period during which resource consumption can exceed the quota without immediate enforcement. Soft limits are useful when you want to gradually control resource usage.

5. **Status and Monitoring**: Kubernetes provides the capability to monitor the status of ResourceQuotas through the Kubernetes API. This allows administrators to track resource consumption within namespaces and enforce resource quotas effectively.

6. **Resource Requests**: ResourceQuotas work in conjunction with resource requests defined in Pod specifications. Resource requests specify the minimum amount of resources a Pod needs. The sum of resource requests for all Pods in a namespace should be within the ResourceQuota limits.

## Create ResourceQuota
You can associate a ResourceQuota with a specific namespace by setting the `spec.namespace` field in the ResourceQuota YAML.

```yaml
# kubectl create quota my-quota --hard=cpu=1,memory=1G,pods=2,services=3,replicationcontrollers=2,resourcequotas=1,secrets=5,persistentvolumeclaims=10 -n=dev --output=yaml --dry-run=client

apiVersion: v1              # Specifies the Kubernetes API version being used (v1 for this example).
kind: ResourceQuota         # Defines the kind of resource being created, in this case, a ResourceQuota.

metadata:
  creationTimestamp: null   # This field represents the timestamp when this ResourceQuota was created (null in this case).
  name: my-quota            # Specifies the name of this ResourceQuota as "my-quota".
  namespace: dev            # Indicates that this ResourceQuota is applied to the "dev" namespace.

spec:
  hard:
    cpu: "1"                        # Sets a hard limit of 1 CPU unit that can be consumed in the "dev" namespace.
    memory: 1G                      # Sets a hard limit of 1 gigabyte of memory that can be consumed in the "dev" namespace.
    persistentvolumeclaims: "10"    # Limits the number of PersistentVolumeClaims to 10 in the "dev" namespace.
    pods: "2"                       # Limits the number of Pods to 2 in the "dev" namespace.
    replicationcontrollers: "2"     # Limits the number of ReplicationControllers to 2 in the "dev" namespace.
    resourcequotas: "1"             # Limits the number of ResourceQuotas to 1 in the "dev" namespace.
    secrets: "5"                    # Limits the number of Secrets to 5 in the "dev" namespace.
    services: "3"                   # Limits the number of Services to 3 in the "dev" namespace.

# The "status" field is left empty, as it's typically used by Kubernetes to provide information about the current status of the object.
status: {}
```

Use `kubectl top` or monitoring tools like Prometheus to monitor resource usage and ensure compliance with ResourceQuotas.

**Note:** Ensure you have the appropriate RBAC permissions to manage ResourceQuotas in the desired namespace. 

## ResourceQuota Resource Types
- `cpu`: CPU resource limit.
- `memory`: Memory resource limit.
- `pods`: Maximum number of pods.
- `requests.cpu`: Minimum CPU request.
- `requests.memory`: Minimum memory request.
- `limits.cpu`: Maximum CPU limit.
- `limits.memory`: Maximum memory limit.

## Resource Requests vs Limits

### Resource Requests

Resource requests represent the amount of resources that a container initially requires to run. They serve as a means of informing the Kubernetes scheduler about the container's resource requirements. When a container specifies its resource requests, Kubernetes uses this information to make intelligent scheduling decisions, placing the container on a node with sufficient resources to meet its needs.

Example: Consider a container that runs a CPU-intensive task. By specifying a CPU request of 500 milliCPU (0.5), you are indicating to Kubernetes that the container requires at least half of a CPU core to run optimally. The scheduler will attempt to find a node that can accommodate this request and schedule the container accordingly.

### Resource Limits

Resource limits specify the maximum amount of resources that a container can consume. They are used to prevent a container from using more resources than allocated, which helps maintain stability and fairness within the cluster. Setting resource limits is important to prevent a single container from monopolizing resources and impacting the performance of other containers.

Example: Let's consider a container that runs a memory-intensive application. By setting a memory limit of 1 gigabyte (1Gi), you are restricting the container from using more than the allocated memory. If the application tries to consume more memory, Kubernetes will take action, such as terminating the container or throttling its resource usage.

## Units

### CPU resource units
Limits and requests for CPU resources are measured in cpu units. In Kubernetes, 1 CPU unit is equivalent to 1 physical CPU core, or 1 virtual core, depending on whether the node is a physical host or a virtual machine running inside a physical machine.

Fractional requests are allowed. When you define a container with `spec.containers[].resources.requests.cpu` set to 0.5, you are requesting half as much CPU time compared to if you asked for 1.0 CPU. For CPU resource units, the quantity expression 0.1 is equivalent to the expression 100m, which can be read as "one hundred millicpu". Some people say "one hundred millicores", and this is understood to mean the same thing.

### Memory resource units
Limits and requests for memory are measured in bytes. You can express memory as a plain integer or as a fixed-point number using one of these quantity suffixes: E, P, T, G, M, k. You can also use the power-of-two equivalents: Ei, Pi, Ti, Gi, Mi, Ki. For example, the following represent roughly the same value:

```
128974848, 129e6, 129M,  128974848000m, 123Mi
```

## Examples
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: my-image
      resources:
        requests:
          cpu: "500m"
          memory: "1Gi"
        limits:
          cpu: "1"
          memory: "12Gi"
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: elephant
spec:
  containers:
  - name: mem-stress
    image: polinux/stress
    imagePullPolicy: Always
    command:
    - stress
    args:
    - --vm
    - "1"
    - --vm-bytes
    - 15M
    - --vm-hang
    - "1"
    resources:
      limits:
        memory: 20Mi
      requests:
        memory: 5Mi
```

## Commands

Certainly! Below is a Kubernetes `ResourceQuota` cheatsheet that provides you with essential `kubectl` commands and information for managing `ResourceQuota` objects:

### Kubernetes ResourceQuota Cheatsheet

**1. Create a ResourceQuota:**
```bash
Certainly! Here are comments for each of the provided `kubectl` commands:

```bash
# Create a resource quota named "my-quota" with specified resource limits for CPU, memory, and pods.
kubectl create resourcequota my-quota --hard=cpu=2,memory=4Gi,pods=10

# List all resource quotas in the current Kubernetes cluster's namespace.
kubectl get resourcequota

# Provide detailed information about the "resourcequota_name" resource quota.
kubectl describe resourcequota <resource_quota_name>

# Open the resource quota named "resource_quota_name" for editing in the default text editor.
kubectl edit resourcequota <resource_quota_name>

# Delete the resource quota named "resource_quota_name" from the current namespace.
kubectl delete resourcequota <resource_quota_name>

# Apply the configuration defined in the "resourcequota.yaml" file to the current namespace.
kubectl apply -f resourcequota.yaml

# Provide detailed information about the specified namespace's resource quotas.
kubectl describe namespace <namespace_name>
```

Replace `<resource_quota_name>` and `<namespace_name>` with the actual names you intend to use when executing these commands.
