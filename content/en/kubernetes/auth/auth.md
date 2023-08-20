---
title: "Auth"
description: ""
date: 2023-08-20T11:16:27Z
lastmod: 2023-08-20T11:16:27Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "auth"
weight: 1100
toc: true
---


## Can I?

`kubectl auth can-i` is a powerful tool to determine whether a user, group, or service account has permissions to perform specific actions on Kubernetes resources.

Check if a User Can Perform an Action in a Namespace
```bash
kubectl auth can-i <verb> <resource> --namespace=<namespace> --as <user>
kubectl auth can-i get pods --namespace=my-namespace --as john
```

Check if a User Can Perform an Action on a Cluster-Wide Resource
```bash
kubectl auth can-i <verb> <resource> --as <user>
kubectl auth can-i get nodes --as admin
```

Check if a Service Account Can Perform an Action in a Namespace
```bash
kubectl auth can-i <verb> <resource> --namespace=<namespace> --as=system:serviceaccount:<namespace>:<service-account-name>
kubectl auth can-i create pods --namespace=my-namespace --as=system:serviceaccount:my-namespace:my-serviceaccount
```

Check if a Group Can Perform an Action in a Namespace
```bash
kubectl auth can-i <verb> <resource> --namespace=<namespace> --as=group:<group-name>
kubectl auth can-i list services --namespace=my-namespace --as=group:developers
```

Check if a User Can Perform an Action on a Specific Resource in a Namespace
```bash
kubectl auth can-i <verb> <resource>/<resource-name> --namespace=<namespace> --as <user>
kubectl auth can-i delete configmap/my-configmap --namespace=my-namespace --as alice
```

Check if a User Can Perform an Action on a Resource Using a Specific RoleBinding
```bash
kubectl auth can-i <verb> <resource>/<resource-name> --namespace=<namespace> --as <user> --all-namespaces
kubectl auth can-i update deployment/my-deployment --namespace=my-namespace --as bob --all-namespaces
```

By using `kubectl auth can-i`, you can quickly verify and troubleshoot RBAC policies in your Kubernetes cluster to ensure proper access control.
