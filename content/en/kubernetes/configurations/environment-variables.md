---
title: "Environment Variables"
description: ""
date: 2023-07-26T07:14:08+02:00
lastmod: 2023-07-26T07:14:08+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1020
toc: true
---

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp-color
spec:
  containers:
  - name: simple-webapp-color
    image: simple-webapp-color
    ports:
      - containerPort: 8080
    env:
      - name: APP_COLOR
        value: pink
```

```shell
$ docker run -e APP_COLOR=pink simple-webapp-color
```

# ENV Value Types:
We can get environment variables in 3 different ways:

## 1. Plain Key Value
```yaml
env:
  - name: APP_COLOR
    value: pink
```

## 2. ConfigMaps


### Imperative
From key value pair in command line
```shell
$ kubectl create configmap \
    app-config2 --from-literal=APP_COLOR=blue \
               --from-literal=APP_MOD=prod
```
From key value pair file
```shell
$ kubectl create configmap \
    app-config --from-file=app_config.properties
```
```shell
$ kubectl create -f config-map.yaml
$ kubectl get configmaps
$ kubectl get cm
$ kubectl describe configmaps
```

### Delcarative

```yaml
# my-config-map.yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  database-url: mysql://localhost:3306/mydb
  api-key: ABC123
```
Here's an example of a Pod that consumes the ConfigMap as environment variables:

```yaml
# my-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-app-image
    envFrom:
    - configMapRef:
        name: my-configmap
```
In the above example, the Pod is associated with the ConfigMap my-configmap using the envFrom field. This makes all the key-value pairs in the ConfigMap available as environment variables in the Pod.

Alternatively, you can also consume individual values from the ConfigMap using the env field:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-app-image
    env:
    - name: DATABASE_URL
      valueFrom:
        configMapKeyRef:
          name: my-configmap
          key: database-url
    - name: API_KEY
      valueFrom:
        configMapKeyRef:
          name: my-configmap
          key: api-key
```
In the above example, the Pod consumes the database-url and api-key values from the ConfigMap and sets them as individual environment variables.

### Using configmaps

1. Inject a single evn variable from configmap
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: dapi-test-pod
  spec:
    containers:
      - name: test-container
        image: gcr.io/google_containers/busybox
        command: [ "/bin/sh", "-c", "env" ]
        env:
          - name: SPECIAL_LEVEL_KEY
            valueFrom:
              configMapKeyRef:
                name: app-config
                key: APP_COLOR
                optional: true # mark the variable as optional
    restartPolicy: Never
  ```
2. Inject the all of the data in configmap
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: dapi-test-pod
  spec:
    containers:
      - name: test-container
        image: registry.k8s.io/busybox
        command: [ "/bin/sh", "-c", "env" ]
        envFrom:
        - configMapRef:
            name: special-config
  ```
3. Volume
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: dapi-test-pod
  spec:
    containers:
      - name: test-container
        image: registry.k8s.io/busybox
        command: [ "/bin/sh", "-c", "ls /etc/config/" ]
        volumeMounts:
        - name: config-volume
          mountPath: /etc/config
    volumes:
      - name: config-volume
        configMap:
          # Provide the name of the ConfigMap containing the files you want
          # to add to the container
          name: special-config
    restartPolicy: Never
  ```

## 3. Secrets


### Imperative
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

### Declarative
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
