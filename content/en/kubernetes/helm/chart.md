---
title: "Chart"
description: ""
date: 2023-09-05T15:51:42Z
lastmod: 2023-09-05T15:51:42Z
draft: true
images: []
---

Creating a Helm chart involves defining the structure, configuration values, and Kubernetes resources required for your application. In this section, I'll guide you through creating a simple Helm chart and explain the template files.

## Chart Structure

A typical Helm chart structure consists of the following directories and files:

- `SomeChart/`
  - `charts/`: Subcharts if any. Contains information about the chart, dependencies, version, etc.
  - `crds/`: Contains custom resource definitions (CRDs).
  - `templates/`: Templates which are passed through the template engine. These templates incorporate the contents opf the values.yaml file.
  - `templates/NOTES.txt`: This plain text file is printed out post-installation and when viewing release status.
  - `values.yaml`: Contains predefined (default) values which will be passed to the templates. Values in this file can be overriden by values passed on the command line during installation.
  - `values.schema.json`: Provides an optional schema for the values.yaml file.
  - `Chart.yaml`: Chart metadata.
  - `LICENSE`: Contains lisense information, if any.
  - `README.md`: Markdown-formatted chart description, optiions, etc.

## Creating a Helm Chart

### Initialize a New Helm Chart

To create a new Helm chart, you can use the `helm create` command. For example, let's create a chart for a hypothetical web application called "mywebapp":

```bash
$ helm create mywebapp
$ tree ./mywebapp -a
.
├── charts
├── Chart.yaml
├── .helmignore
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

### View the Manifest

A manifest refers to a declarative configuration file that describes the desired state of a Kubernetes resource. Manifests are typically written in YAML or JSON format and are used to define various components and objects within a Kubernetes cluster. These objects can include pods, services, deployments, replica sets, config maps, secrets, and more. To view the manifest for the newly created package run:

```bash
$ helm install mywebapp-release ./mywebapp --dry-run
NAME: mywebapp-release
LAST DEPLOYED: Wed Sep  6 07:18:13 2023
NAMESPACE: default
STATUS: pending-install
REVISION: 1
HOOKS:
---
# Source: mywebapp/templates/tests/test-connection.yaml
apiVersion: v1
kind: Pod
metadata:
  name: "mywebapp-release-test-connection"
  labels:
    helm.sh/chart: mywebapp-0.1.0
    app.kubernetes.io/name: mywebapp
    app.kubernetes.io/instance: mywebapp-release
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
  annotations:
    "helm.sh/hook": test
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args: ['mywebapp-release:80']
  restartPolicy: Never
MANIFEST:
---
# Source: mywebapp/templates/serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: mywebapp-release
  labels:
    helm.sh/chart: mywebapp-0.1.0
    app.kubernetes.io/name: mywebapp
    app.kubernetes.io/instance: mywebapp-release
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
---
# Source: mywebapp/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: mywebapp-release
  labels:
    helm.sh/chart: mywebapp-0.1.0
    app.kubernetes.io/name: mywebapp
    app.kubernetes.io/instance: mywebapp-release
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: mywebapp
    app.kubernetes.io/instance: mywebapp-release
---
# Source: mywebapp/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mywebapp-release
  labels:
    helm.sh/chart: mywebapp-0.1.0
    app.kubernetes.io/name: mywebapp
    app.kubernetes.io/instance: mywebapp-release
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: mywebapp
      app.kubernetes.io/instance: mywebapp-release
  template:
    metadata:
      labels:
        app.kubernetes.io/name: mywebapp
        app.kubernetes.io/instance: mywebapp-release
    spec:
      serviceAccountName: mywebapp-release
      securityContext:
        {}
      containers:
        - name: mywebapp
          securityContext:
            {}
          image: "nginx:1.16.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
            {}

NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mywebapp,app.kubernetes.io/instance=mywebapp-release" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
```

### Information About the Chart

```bash
# show all information of the chart
helm show all ./mywebapp

# show the chart's definition
helm show chart ./mywebapp

# show the chart's CRDs
helm show crds ./mywebapp

# show the chart's README
helm show readme ./mywebapp

# show the chart's values
helm show values ./mywebapp
```

You can show information for the fetched `tgz` packages like `prometheus-24.3.0.tgz` instead of `./mywebapp`

### Updating the Chart Values

Values in Helm charts allow you to customize the chart's behavior during installation. You can specify values in a `values.yaml` file or override them using the `--set` flag during installation:

#### Using `values.yaml`

First you need to create a new yaml file to override the `values.yaml` file inside the package:

```yaml
# custom-values.yaml
image:
  tag: latest
```

Then use the `-f` flag to override the the default `values.yaml` file:

```bash
$ helm install mywebapp-release ./mywebapp -f custom-values.yaml --dry-run
...
      containers:
        - name: mywebapp
          securityContext:
            {}
          image: "nginx:latest"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
...
```


#### Using `--set flag`
```bash
helm install mywebapp-release ./mywebapp --set image.tag=latest --set service.type=NodePort --dry-run
```

### Install the Chart

You can install your chart into a Kubernetes cluster using the `helm install` command. For example:

```bash
helm install mywebapp-release ./mywebapp
```

Here, `mywebapp-release` is the name you give to the release, and `./mywebapp` is the path to your chart directory.

That's it! You've created a basic Helm chart. You can now customize and expand it to suit your application's needs. To deploy the chart with specific configuration values, use the `--set` flag followed by key-value pairs or provide a custom values file with `--values`.

Creating and managing Helm charts can greatly simplify the deployment and management of complex applications in Kubernetes, making it an essential tool for Kubernetes developers and operators.