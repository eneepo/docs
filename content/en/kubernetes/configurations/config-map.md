---
title: "Config Map"
description: ""
date: 2023-07-18T08:19:58+02:00
lastmod: 2023-07-18T08:19:58+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1015
toc: false
---

## Delcarative

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

## Imperative
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
## Using the configmap in a pod defenition:

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