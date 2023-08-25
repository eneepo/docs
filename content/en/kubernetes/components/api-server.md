---
title: "Api Server"
description: ""
date: 2023-08-22T17:12:38Z
lastmod: 2023-08-22T17:12:38Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "components"
weight: 1300
toc: true
---

The API server is a critical component that serves as the central management point and communication hub for the entire cluster. It acts as the front-end to the Kubernetes control plane and exposes the Kubernetes API, which allows users, administrators, and various Kubernetes components to interact with the cluster. Here are the key aspects of the API server:

1. **API Endpoint**: The API server provides an HTTP(S) endpoint that clients use to interact with the Kubernetes cluster. This API is used for creating, updating, and deleting Kubernetes resources such as pods, services, and deployments. Clients can include command-line tools like `kubectl`, automation scripts, and other Kubernetes components.

2. **Authentication and Authorization**: The API server handles authentication and authorization of requests. It verifies the identity of users and checks if they have the necessary permissions to perform the requested actions. This is crucial for securing the cluster.

3. **Resource Validation**: The API server performs validation of requests to ensure they adhere to Kubernetes' schema and policies. It checks whether the requested resources are well-formed and within acceptable limits.

4. **Admission Control**: Admission controllers are plugins that enable fine-grained control over resource creation and mutation. They can enforce policies like resource quotas, security policies, and custom validation.

5. **Etcd Interaction**: The API server interacts with the cluster's etcd datastore to read and write cluster state information. It stores configuration data, such as object definitions (e.g., Pods, Services), in etcd.

6. **Component Communication**: Other Kubernetes components interact with the API server to manage and monitor the cluster:

   - **Controller Manager**: The controller manager watches the API server for changes in the desired state of resources and ensures the actual state matches the desired state. For example, it manages the replication controller, replica set, and deployment controllers.

   - **Scheduler**: The scheduler interacts with the API server to monitor the cluster's capacity and schedule Pods onto suitable nodes based on resource requirements and constraints.

   - **kubelet**: Each node in the cluster runs a kubelet, which communicates with the API server to ensure that the Pods scheduled to run on that node are actually running and healthy.

   - **kube-proxy**: The kube-proxy component communicates with the API server to maintain network rules on nodes. It ensures that network traffic is correctly routed to Pods.

   - **Addon Services**: Addon services like the Horizontal Pod Autoscaler (HPA) and Ingress Controllers also interact with the API server to read configuration and make decisions.

7. **Watch and Notify**: The API server supports watch functionality, allowing clients to subscribe to changes in resources. This is essential for building real-time applications or controllers that react to changes in the cluster.

8. **Logging and Auditing**: The API server can be configured to log its activities and perform auditing to track who accessed the API server and what actions were taken.

9. **Versioning**: The API server supports multiple API versions, allowing for backward compatibility and smoother upgrades.

10. **Extensions**: It provides a mechanism for extending the Kubernetes API with Custom Resource Definitions (CRDs). This enables users to define and manage custom resources beyond the built-in Kubernetes resource types.

## Commands

```bash
# View kube-controller-manager options (manual installation)
cat /etc/systemd/system/kube-apiserver.service

# View kube-controller-manager options (kubeadm installation)
cat /etc/kubernetes/manifests/kube-apiserver.yaml

# View api-server options
cat /etc/systemd/system/kube-apiserver.service

# View api-server options by listing the process on the master node and searching for kube-apiserver
ps -aux | grep kube-apiserver
```
