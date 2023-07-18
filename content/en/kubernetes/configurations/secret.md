---
title: "Secret"
description: ""
date: 2023-07-18T08:20:43+02:00
lastmod: 2023-07-18T08:20:43+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1020
toc: false
---

1. Imperative way to create secrets
```shell
$ kubectl create secret generic\
    app-secret --from-literal=DB_HOST=mysql \
               --from-literal=DB_USER=root \
               --from-literal=DB_PASS=pass
```
```shell
$ kubectl create secret generic\
    app-secret --from-file=app_secret.properties
```
```shell
$ kubectl get secrets
$ kubectl describe secrets
$ kubectl get secret app-secret -o yaml
```
2. Declarative way to create secrets
First you must encode the secrets to base64
```shell
$ echo -n "mysql" | base64
bX1zcWw=
```
To decode the encoded values
```shell
$ echo -n "bX1zcWw=" | base64 --decode
mysql
```
mysecret.yaml
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  DB_HOST: bX1zcWw=
  DB_USER: YWRtaW4=
  DB_PASS: MWYyZDFlMmU2N2Rm
```
```shell
$ kubectl apply -f mysecret.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-test-pod
spec:
  containers:
    - name: test-container
      image: registry.k8s.io/busybox
      command: [ "/bin/sh", "-c", "env" ]
      envFrom:
      - secretRef:
          name: mysecret
  restartPolicy: Never
```
[Enabling Encryption at Rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) for Secrets so they are stored encrypted in ETCD.
The way kubernetes handles secrets. Such as:
  * A secret is only sent to a node if a pod on that node requires it.
  * Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
  * Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.
