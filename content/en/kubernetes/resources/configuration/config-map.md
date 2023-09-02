---
title: "Config Map"
description: ""
date: 2023-07-26T07:14:08+02:00
lastmod: 2023-07-26T07:14:08+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1050
---

A ConfigMap is a Kubernetes resource that stores configuration data in key-value pairs. This data can be used by Pods or other resources to configure various aspects of an application, such as environment variables, command-line arguments, or configuration files. ConfigMaps are typically used for non-sensitive configuration data like application settings, database connection strings, or external service URLs.

## Examples

### Example 1: `from-literal`

1. Creating configmap from `from-literal` imperative command

```yaml
# kubectl create configmap my-config --from-literal=NGINX_HOST=example.com --from-literal=NGINX_PORT=80

apiVersion: v1
data:
  APP_ENV: PROD
  LOG_LEVEL: info
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: my-config
```

2. Once you've created a ConfigMap, you can use it in a Pod to inject configuration data into containers running within that Pod. Here's an example of a Pod definition that uses the ConfigMap we created earlier:

```yaml
# This YAML file defines a Kubernetes Pod resource.
apiVersion: v1
kind: Pod

metadata:
  labels:
    run: web
  name: web

spec:
  containers:                         # This section defines the containers running within the Pod.
  - env:                              # The "env" field specifies environment variables to be set within the container.

    - name: NGINX_HOST                # Environment variable "NGINX_HOST" is defined here.
      valueFrom:                      # It is sourced from a ConfigMap key reference named "my-config".
        configMapKeyRef:
          name: my-config             # The key within the ConfigMap from which the value of "NGINX_HOST" is taken.
          key: NGINX_HOST

    - name: NGINX_PORT                # Similarly, environment variable "NGINX_PORT" is defined here.
      valueFrom:                      # It is sourced from a ConfigMap key reference named "my-config".
        configMapKeyRef:
          name: my-config             # The key within the ConfigMap from which the value of "NGINX_PORT" is taken.
          key: NGINX_PORT
          optional: true              # Mark the variable as optional
  
    image: nginx
    name: web
```

### Example 2: `--from-file`

Creating a ConfigMap in Kubernetes using the `--from-file` flag allows you to populate the ConfigMap with data from one or more files on your local system. Each file's content will be stored as a key-value pair in the ConfigMap. Here's an example of how to create a ConfigMap using the `--from-file` flag:

Suppose you have two configuration files, `app_config.txt` and `db_config.txt`, and you want to create a ConfigMap from these files.

1. Create the `app_config.txt` and `db_config.txt` files on your local system with some sample configuration data:

`app_config.txt`:
```plaintext
APP_ENV=production
LOG_LEVEL=info
```

`db_config.txt`:
```plaintext
DATABASE_URL=jdbc:mysql://db-server:3306/mydb
DB_USERNAME=myuser
DB_PASSWORD=mypassword
```

2. Use the `kubectl create configmap` command with the `--from-file` flag to create the ConfigMap:

```bash
kubectl create configmap my-config --from-file=app_config.txt --from-file=db_config.txt
```

In this command:

- `my-config` is the name of the ConfigMap you want to create.
- `--from-file` specifies that you're populating the ConfigMap from files.
- `app_config.txt` and `db_config.txt` are the file paths from which the data will be read.

After running this command, Kubernetes will create a ConfigMap named `my-config` with key-value pairs based on the content of the specified files.

You can verify the ConfigMap's contents using the `kubectl describe` command:

```bash
kubectl describe configmap my-config
```

You should see output similar to the following:

```plaintext
Name:         my-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
app_config.txt:
----
APP_ENV=production
LOG_LEVEL=info

db_config.txt:
----
DATABASE_URL=jdbc:mysql://db-server:3306/mydb
DB_USERNAME=myuser
DB_PASSWORD=mypassword

Events:  <none>
```

Now, you have a ConfigMap named `my-config` containing the configuration data from the `app_config.txt` and `db_config.txt` files. You can use this ConfigMap to inject configuration data into your Pods as needed, similar to the example provided in the previous example.

### Example 3: Inject the all of the data in configmap

```yaml
apiVersion: v1
kind: Pod

metadata:
  labels:
    run: web
  name: web

spec:
  containers:                       # List the containers that should run in this Pod. In this case, there is one container.
    image: nginx
    name: web
    envFrom:                        # Define environment variables for this container from a ConfigMap.
    - configMapRef:                 # Specify the name of the ConfigMap to use as a source for environment variables, which is "my-config."
        name: my-config
```

### Example 4: Using volume


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

## Commands
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
