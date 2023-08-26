---
title: "Deployment"
description: "A Deployment is a higher-level resource that provides declarative updates for managing the state of your application."
date: 2023-07-18T07:57:45+02:00
lastmod: 2023-07-18T07:57:45+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "pod-management"
weight: 1015
toc: true
---

Using Deployments, you can abstract away the low-level details of managing individual pods and focus on maintaining the desired state of your application. Deployments build on top of ReplicaSets, offering additional features and capabilities for managing application updates and rollbacks. Deployments are a crucial tool for managing production-ready applications in Kubernetes, as they provide a safe and automated way to deploy, update, and scale applications without causing disruptions to the end-users.

```yaml
# kubectl create deployment my-app --image=nginx --port=80 --replicas=3 --dry-run=client -o yaml

apiVersion: apps/v1         # Specifies the Kubernetes API version being used for this resource.
kind: Deployment            # Specifies the kind of resource, which is a Deployment in this case.

metadata:                   # Metadata section contains information about the Deployment.
  creationTimestamp: null     # The creation timestamp of this Deployment is not set.
  labels:
    app: my-app               # Labels are used for identifying and grouping resources; here, the label "app" is set to "my-app".
  name: my-app                # The name of this Deployment is "my-app".

spec:
  replicas: 3                 # Specifies that we want to maintain 3 replicas (copies) of the Pod defined within this Deployment.
  
  selector:
    matchLabels:
      app: my-app             # Selects Pods with the label "app" set to "my-app" for management by this Deployment.

  strategy: {}                # Specifies the update strategy for the Deployment, which is not defined in this case.

  template:
    metadata:
      creationTimestamp: null # The creation timestamp for the Pod template is not set.
      labels:
        app: my-app           # Labels for Pods created from this template are set to "app: my-app".

    spec:
      containers:
      - image: nginx          # Specifies the Docker image to use for the container within the Pod (in this case, Nginx).
        name: nginx           # Names the container as "nginx".
        ports:
        - containerPort: 80   # Opens port 80 within the container for network traffic.
        resources: {}         # Resource limits and requests for the container are not defined in this template.

status: {}                    # The status of the Deployment is not explicitly configured in this YAML.
```

In Kubernetes, managing deployments involves updating, rolling out changes, rolling back changes, handling revisions, and undoing changes. Here's an explanation of each concept with examples:

## Update

When you need to make changes to the configuration of your application, you can update the deployment with new settings. This involves modifying the deployment's template specifications, such as changing the container image version, updating environment variables, or adjusting resource limits.

Example:
Let's say you want to update the image version of your application to a newer release:
```bash
kubectl set image deployment/myapp-deployment myapp-container=myapp:v2
```

## Rollout
A rollout is the process of applying an update to a deployment or replicaset in a controlled manner, ensuring that the new version of the application is gradually and seamlessly deployed while monitoring its status. Kubernetes provides strategies like "RollingUpdate" that allow you to specify how many replicas should be available at any given time during the rollout.

Example:
Perform a controlled rollout of a new version of your application:
```bash
kubectl rollout status deployment/myapp-deployment
```

## Rollback
In case an update introduces unexpected issues or errors, you might need to revert to a previous known good state. Kubernetes allows you to roll back a deployment to a specific revision, effectively undoing the last update.

Example:
Roll back the deployment to the previous revision:
```bash
kubectl rollout undo deployment/myapp-deployment
   ```

## Revision
A revision in Kubernetes refers to a specific version of a deployment. It encapsulates the configuration and state of the application at a given point in time. Each rollout or update creates a new revision.

Example:
List all revisions of a deployment to see their details:
```bash
kubectl rollout history deployment/myapp-deployment
```

## Undo a Change
Undoing a change involves reverting to a previous revision to fix issues caused by an update. This is particularly useful when a new deployment version causes problems in a production environment.

Example:
If the latest update is causing issues, undo to the previous revision:
```bash
kubectl rollout undo deployment/myapp-deployment --to-revision=2
```

These concepts and commands are essential for effectively managing deployments in Kubernetes, ensuring your applications are updated, rolled out, and rolled back in a controlled and efficient manner.

## Status of a Revision

You can check the status of each revision individually by using the --revision flag:

```shell
kubectl rollout undo deployment/myapp-deployment --revision=1
```

## Commands

```bash
# Display help and usage information for the 'create deployment' command.
kubectl create deployment --help

# Create a deployment using the specifications defined in 'deployment-definition.yaml'.
kubectl create -f deployment-definition.yaml

# Apply the configurations specified in 'deployment-definition.yaml' to the deployment.
kubectl apply -f deployment-definition.yaml

# Replace the current deployment with the configurations from 'deployment-definition.yaml'.
kubectl replace -f deployment-definition.yaml

# Scale the deployment defined in 'deployment-definition.yaml' to have 6 replicas.
kubectl scale --replicas=6 -f deployment-definition.yaml

# Scale the 'myapp-deployment' to have 6 replicas.
kubectl scale --replicas=6 deployment myapp-deployment

# Get information about deployments in the current namespace.
kubectl get deployment

# Get information about replicaset(s) in the current namespace.
kubectl get replicaset

# Get information about all resources (pods, services, deployments, etc.) in the current namespace.
kubectl get all

# Provide detailed information about the 'myapp-deployment' deployment.
kubectl describe deployment myapp-deployment
```