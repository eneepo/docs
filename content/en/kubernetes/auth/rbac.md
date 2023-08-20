---
title: "RBAC"
description: ""
date: 2023-08-20T11:02:34Z
lastmod: 2023-08-20T11:02:34Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "auth"
weight: 1200
toc: true
---

RBAC, or Role-Based Access Control, is a security mechanism in Kubernetes that allows you to control who can access and perform actions on resources within a Kubernetes cluster. RBAC helps you define and manage permissions for users and service accounts, ensuring that only authorized entities can interact with the cluster. RBAC is crucial for securing your Kubernetes deployments.

RBAC in Kubernetes operates through the following key components:

 **Roles and ClusterRoles Roles are used to define a set of permissions within a specific namespace, while ClusterRoles define permissions across the entire cluster.

 **RoleBindings and ClusterRoleBindings These resources associate Roles or ClusterRoles with users, groups, or service accounts. RoleBindings are used within a namespace, while ClusterRoleBindings are cluster-wide.

## Example

1. Create a Role called "developer" in a YAML file (e.g., `developer-role.yaml`):

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: your-namespace
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "get", "create", "update", "delete"]
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
  resourceNames: ["<configmap-name>"]  # Replace with the desired ConfigMap name
```

In this YAML manifest:

- `apiVersion` specifies the RBAC API version.
- `kind` defines that it's a Role.
- `metadata` contains metadata about the Role, including its name and the namespace where it should be applied.
- `rules` define the permissions. For Pods, the developer can list, get, create, update, and delete. For ConfigMaps, the developer can only create.

2. Create a RoleBinding to bind the "developer" role to a user, group, or service account. Here's an example YAML for a RoleBinding (e.g., `developer-rolebinding.yaml`):

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-binding
  namespace: your-namespace
subjects:
- kind: User  # You can also use 'Group' or 'ServiceAccount' depending on your use case.
  name: developer-user  # Replace with the actual username, group name, or service account name.
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

In this YAML manifest:

- `apiVersion` specifies the RBAC API version.
- `kind` defines that it's a RoleBinding.
- `metadata` contains metadata about the RoleBinding, including its name and the namespace where it should be applied.
- `subjects` specify the users, groups, or service accounts that will have the "developer" role. Replace `"developer-user"` with the actual user, group, or service account name.
- `roleRef` links the RoleBinding to the "developer" Role.

After creating these YAML files, you can apply them to your Kubernetes cluster using the kubectl apply command:

```bash
kubectl apply -f developer-role.yaml
kubectl apply -f developer-rolebinding.yaml
```

With these configurations in place, the user or entity specified in the RoleBinding will have the specified permissions (list, get, create, update, delete) for the specified resource (in this example, "pods") within the specified namespace.

## Commands

### Roles and RoleBindings Cheatsheet

Create a Role
```bash
kubectl create role <role-name> --verb=<verbs> --resource=<resources> --namespace=<namespace>
kubectl create role developer --verb=list,get,create,update,delete --resource=pods --namespace=my-namespace
```

Create a ClusterRole
```bash
kubectl create clusterrole <clusterrole-name> --verb=<verbs> --resource=<resources>
kubectl create clusterrole developer --verb=list,get,create,update,delete --resource=pods
```

Create a RoleBinding
```bash
kubectl create rolebinding <binding-name> --role=<role-name> --user=<user> --namespace=<namespace>
kubectl create rolebinding developer-binding --role=developer --user=john --namespace=my-namespace
```

Create a ClusterRoleBinding
```bash
kubectl create clusterrolebinding <binding-name> --clusterrole=<clusterrole-name> --user=<user>
kubectl create clusterrolebinding developer-binding --clusterrole=developer --user=john
```

Get Roles and RoleBindings
```bash
kubectl get roles --namespace=<namespace>
kubectl get rolebindings --namespace=<namespace>
```

Get ClusterRoles and ClusterRoleBindings

```bash
kubectl get clusterroles
kubectl get clusterrolebindings
```

Describe a Role or RoleBinding

```bash
kubectl describe role <role-name> --namespace=<namespace>
kubectl describe rolebinding <binding-name> --namespace=<namespace>
```

Describe a ClusterRole or ClusterRoleBinding

```bash
kubectl describe clusterrole <clusterrole-name>
kubectl describe clusterrolebinding <binding-name>
```

Delete a Role or RoleBinding

```bash
kubectl delete role <role-name> --namespace=<namespace>
kubectl delete rolebinding <binding-name> --namespace=<namespace>
```

Delete a ClusterRole or ClusterRoleBinding

```bash
kubectl delete clusterrole <clusterrole-name>
kubectl delete clusterrolebinding <binding-name>
```
