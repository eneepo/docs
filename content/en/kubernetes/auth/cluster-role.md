---
title: "Cluster Role"
description: ""
date: 2023-08-20T13:48:12Z
lastmod: 2023-08-20T13:48:12Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "auth"
weight: 1220
toc: true
---
A ClusterRole is a set of permissions that can be assigned to users, groups, or service accounts at the cluster level. ClusterRoles are used to define what actions are allowed on cluster-wide resources. When you want to grant permissions that span across multiple namespaces or apply to cluster-wide resources like nodes, ClusterRoles are the appropriate tool to use.

## Example
Here's an example of a ClusterRole that grants permissions for various resources:

### ClusterRole
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin
rules:
  # Allow listing all pods in all namespaces
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]

  # Allow creating, updating, and deleting ConfigMaps in any namespace
  - apiGroups: [""]
    resources: ["configmaps"]
    verbs: ["create", "update", "delete"]

  # Allow listing and watching events in all namespaces
  - apiGroups: ["events.k8s.io"]
    resources: ["events"]
    verbs: ["list", "watch"]

  # Allow accessing nodes and describing nodes
  - apiGroups: [""]
    resources: ["nodes"]
    verbs: ["get", "describe"]

  # Allow getting, creating, updating, and deleting custom resources
  - apiGroups: ["example.com"]
    resources: ["customresources"]
    verbs: ["get", "create", "update", "delete"]

  # Allow getting and listing namespaces
  - apiGroups: [""]
    resources: ["namespaces"]
    verbs: ["get", "list"]

  # Allow viewing and managing roles and rolebindings in all namespaces
  - apiGroups: ["rbac.authorization.k8s.io"]
    resources: ["roles", "rolebindings"]
    verbs: ["get", "list", "create", "update", "delete"]
```

In this example, a ClusterRole named `cluster-admin` is defined. This ClusterRole includes a set of rules that grant permissions for various resources and actions:

1. **Pods**: It allows listing (getting and listing) all pods in all namespaces.

2. **ConfigMaps**: It permits creating, updating, and deleting ConfigMaps across all namespaces.

3. **Events**: It allows listing and watching events in all namespaces.

4. **Nodes**: This rule grants permission to get and describe nodes, which are non-namespaced resources.

5. **Custom Resources**: If you have custom resources defined in your cluster under the `example.com` API group, this rule allows getting, creating, updating, and deleting them.

6. **Namespaces**: It allows getting and listing namespaces, which are non-namespaced resources.

7. **Role and RoleBinding**: It permits viewing and managing roles and role bindings in all namespaces.

Once you've defined the ClusterRole, you can bind it to users or service accounts using ClusterRoleBindings to grant them the specified permissions across the entire cluster. Remember to use ClusterRoles judiciously, as they grant broad access, and it's essential to follow the principle of least privilege to enhance security in your Kubernetes clusters.

### ClusterRoleBinding
Create a ClusterRoleBinding to bind the `cluster-admin` ClusterRole to a user, service account, or group of users. Here's an example of a ClusterRoleBinding:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-binding
subjects:
  - kind: User  # You can also use 'ServiceAccount' or 'Group' here
    name: your-username  # Replace with the actual user, service account, or group name
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin  # Refers to the ClusterRole you defined earlier
  apiGroup: rbac.authorization.k8s.io
```

In this example:

- `name` is the name of the ClusterRoleBinding, which can be customized to your preference.
- `subjects` specify the entities to which you want to bind the ClusterRole. In this case, we've used a user as an example, but you can also use `ServiceAccount` or `Group` depending on your use case.
- `roleRef` specifies the ClusterRole that you want to bind. Make sure that `name` matches the name of the ClusterRole you defined earlier.

Replace `your-username` with the actual username, service account name, or group name you want to grant the `cluster-admin` permissions to. After creating this ClusterRoleBinding resource, the specified user, service account, or group will have the permissions defined in the `cluster-admin` ClusterRole across the entire cluster.

## Commands

Here's a cheatsheet for using `kubectl` to generate a ClusterRole in Kubernetes:

### Creating a ClusterRole

```bash
# Create a ClusterRole from a YAML file
kubectl create -f clusterrole.yaml

# Create a ClusterRole directly from the command line
kubectl create clusterrole <clusterrole-name> --verb=<verb> --resource=<resource> --resource-name=<resource-name> --api-group=<api-group>
```

### Viewing ClusterRoles

```bash
# List all ClusterRoles in the cluster
kubectl get clusterroles

# Describe a specific ClusterRole
kubectl describe clusterrole <clusterrole-name>
```

### Editing a ClusterRole

```bash
# Edit a ClusterRole
kubectl edit clusterrole <clusterrole-name>
```

### Deleting a ClusterRole

```bash
# Delete a ClusterRole by name
kubectl delete clusterrole <clusterrole-name>

# Delete a ClusterRole using a YAML file
kubectl delete -f clusterrole.yaml
```

### Generating a YAML Template

```bash
# Generate a YAML template for a ClusterRole
kubectl create clusterrole <clusterrole-name> --dry-run=client -o yaml
```
