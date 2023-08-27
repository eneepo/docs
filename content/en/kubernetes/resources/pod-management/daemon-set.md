---
title: "Daemon Set"
description: ""
date: 2023-08-27T19:31:54Z
lastmod: 2023-08-27T19:31:54Z
date: 2023-07-09T16:58:56+02:00
lastmod: 2023-07-09T16:58:56+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "pod-management"
weight: 1013
toc: true
---

A DaemonSet is a resource that ensures that a copy of a specific pod runs on all or a subset of nodes in a cluster. Unlike other controllers like Deployments or ReplicaSets, DaemonSets are designed for tasks that need to run on every node in a cluster, such as monitoring agents, log collectors, or storage daemons.

## Key Notes

1. **Pods on Every Node:** The primary purpose of a DaemonSet is to guarantee that at least one instance of a specified pod runs on every node in the cluster where the DaemonSet is applied. This ensures that a particular task or service is always available on each node.

2. **One Pod Per Node:** DaemonSets are designed to maintain a one-to-one relationship between nodes and pods. If new nodes are added to the cluster, the DaemonSet controller automatically deploys pods to those nodes. Conversely, if nodes are removed, DaemonSets terminate the corresponding pods.

3. **Selectors and Labels:** DaemonSets use node selectors and pod labels to determine which nodes the pods should be scheduled on. This allows you to target specific nodes or groups of nodes in your cluster.

4. **Rolling Updates and Scaling:** Updating a DaemonSet can be tricky since you want to ensure that all nodes are running the same version of the pod. Kubernetes provides mechanisms for performing rolling updates of DaemonSets to handle this scenario gracefully.

5. **Pod Template:** Like other controllers in Kubernetes, DaemonSets use a pod template to define the pod's characteristics, such as the container image, resources, and volumes. This template is used to create and update the pods.

6. **Tolerations and Node Affinity:** DaemonSets can be customized using tolerations and node affinity rules to control which nodes they are scheduled on, allowing for more fine-grained control over placement.

7. **Auto-Remediation:** DaemonSets can automatically remediate pod failures. If a pod goes down for any reason, the DaemonSet controller will detect the failure and reschedule the pod on the same node or another suitable node.

## Use Cases

- **Logging and Monitoring:** DaemonSets are commonly used to deploy agents or collectors for logging (e.g., Fluentd) and monitoring (e.g., Prometheus Node Exporter) on each node.
- **Network Plugins:** Networking plugins like Calico or Weave often utilize DaemonSets to ensure that network components are available on every node.
- **Storage:** Storage solutions such as GlusterFS or Ceph may use DaemonSets to deploy storage-related components.
- **Security:** Security-related tools like intrusion detection systems (IDS) or antivirus agents can be deployed as DaemonSets for comprehensive node-level protection.

## Example

Let's take an example of deploying the Fluentd log collector as a DaemonSet in a Kubernetes cluster. Fluentd is a popular choice for collecting, processing, and forwarding logs.

### 1. Create a Fluentd DaemonSet Manifest

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd
  template:
    metadata:
      labels:
        name: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

In this example manifest:

- We define a DaemonSet named "fluentd" in the "kube-system" namespace.
- The DaemonSet ensures that a pod labeled as "name: fluentd" runs on each node.
- We specify a container running the Fluentd image, along with resource requests and limits.
- Fluentd will collect logs from the /var/log directory on the host and from Docker containers.
- We set a termination grace period of 30 seconds for graceful pod termination.

### 2. Apply the DaemonSet

Apply the DaemonSet to your cluster using `kubectl`:

```bash
kubectl apply -f fluentd-daemonset.yaml
```

### 3. Verify the DaemonSet Pods

You can check the status of the DaemonSet pods:

```bash
kubectl get pods -n kube-system -l k8s-app=fluentd-logging
```

### 4. Monitor Logs

Now, Fluentd will collect and forward logs from each node to a centralized logging system or destination of your choice.
