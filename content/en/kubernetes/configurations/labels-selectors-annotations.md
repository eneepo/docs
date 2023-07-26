---
title: "Labels, Selectors, and Annotations"
description: ""
date: 2023-07-26T07:22:10+02:00
lastmod: 2023-07-26T07:22:10+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1030
toc: true
---
## Labels
Labels are key-value pairs that are attached to Kubernetes objects, such as pods, services, and deployments. They are used to identify and categorize resources. Labels are typically used to specify properties or characteristics of an object, such as its purpose, environment, version, or any other custom attribute.

Let's say you have a set of pods representing a web application, and you want to label them based on their environment and version. You can add the following labels to the pod's metadata:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    tier: frontend
    env: prod
    bu: finance
    version: v1.0.0
```

```shell
$ k get pods --selector tier=frontend
$ k get pods --selector tier=frontend,bu=finance,env=prod # all of the labels
$ k get pods --selector tier=frontend  --selector bu=finance # any of
```
## Annotations
Annotations are similar to labels but provide additional information about an object. They are not used for selecting or filtering resources, but rather for attaching arbitrary metadata to an object. Annotations can be used to store descriptive information, build automation tools, or provide hints to other systems.

Let's say you want to store additional information about a pod, such as the contact person responsible for that pod. You can add annotations to the pod's metadata:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  annotations:
    contact: john@example.com
    buildversion: 1.34
```

## Selectors
Selectors are used to identify and filter resources based on their labels. They enable you to define rules for selecting a specific set of resources that match certain criteria. Selectors are commonly used in conjunction with other Kubernetes objects, such as services, replica sets, and deployments, to define the set of resources they should target.

Let's say you have multiple pods with different labels and you want to create a service that targets only the pods with the `environment=production` label. You can define a selector for the service as follows:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    environment: production
```
In this example, the service will only route traffic to the pods that have the label "environment=production" attached to them.

Labels, annotations, and selectors provide powerful mechanisms for organizing, categorizing, and selecting resources in Kubernetes, enabling you to apply advanced configuration and control to your cluster.
