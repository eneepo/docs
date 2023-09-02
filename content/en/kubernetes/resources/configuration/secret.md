---
title: "Secret"
description: ""
date: 2023-09-02T13:14:41Z
lastmod: 2023-09-02T13:14:41Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1080
---

Secrets are a way to securely store sensitive information, such as passwords, API keys, and tokens, that your applications or services running within pods may need. Kubernetes provides a built-in resource called "Secrets" to help manage and distribute these sensitive data securely. Secrets are encoded in base64 encoding to protect them from casual inspection, but they are not a foolproof security mechanism on their own. They should be used in conjunction with other security best practices.

Here, we'll explore how to create and use secrets in Kubernetes pods, along with some examples.

## Examples

### Example 1: `from-literal`

1. Creating a secret using the kubectl create secret command:

```yaml
# kubectl create secret generic my-secret --from-literal=username=myuser --from-literal=password=mypassword

apiVersion: v1
data:
  password: bXlwYXNzd29yZA==
  username: bXl1c2Vy
kind: Secret
metadata:
  creationTimestamp: null
  name: my-secret
```

2. Here's an example how to use the secrets in previous step in a pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
    - name: my-container
      image: my-image
      env:
        - name: DB_USERNAME
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: username
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: password
```

## Commands
```shell
# Encode the string "mysql" in base64 format and print the result
$ echo -n "mysql" | base64
bX1zcWw=

# Decode the base64 encoded string "bX1zcWw=" and print the original text
$ echo -n "bX1zcWw=" | base64 --decode
mysql

# Create a Kubernetes secret named "app-secret" with three key-value pairs for database configuration
$ kubectl create secret generic \
    app-secret --from-literal=DB_HOST=mysql \
               --from-literal=DB_USER=root \
               --from-literal=DB_PASS=pass

# Create a Kubernetes secret named "app-secret" from a file named "app_secret.properties"
$ kubectl create secret generic \
    app-secret --from-file=app_secret.properties

# List all secrets in the current Kubernetes cluster
$ kubectl get secrets

# Describe the details of all secrets in the current Kubernetes cluster
$ kubectl describe secrets

# Get the details of the "app-secret" secret in YAML format
$ kubectl get secret app-secret -o yaml
```

## Encrption at Rest

[Enabling Encryption at Rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) for Secrets so they are stored encrypted in ETCD.
The way kubernetes handles secrets. Such as:
  * A secret is only sent to a node if a pod on that node requires it.
  * Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
  * Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.
