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
    parent: "resources"
weight: 1020
toc: true
---
A Deployment is a higher-level resource that provides declarative updates for managing the state of your application. It is one of the primary mechanisms used for deploying and managing applications in a Kubernetes cluster. Deployments build on top of ReplicaSets, offering additional features and capabilities for managing application updates and rollbacks.

Using Deployments, you can abstract away the low-level details of managing individual pods and focus on maintaining the desired state of your application. Deployments are a crucial tool for managing production-ready applications in Kubernetes, as they provide a safe and automated way to deploy, update, and scale applications without causing disruptions to the end-users.
```yaml
#deployment-definition.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
spec:
  replicas: 3
  selector:
    matchLabels:
      type: front-end
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
```

## Commands Summary
```shell
k create deployment --help
k create -f deployment-definition.yaml
k apply -f deployment-definition.yaml
k replace -f deployment-definition.yaml
k scale --replicas=6 -f deployment-definition.yaml
k scale --replicas=6 deployment myapp-deployment
k get deployment
k get replicaset
k get all
k describe deployment myapp-deployment
```


## Update, Rollout, and rollback
In the example below, we will first create a simple deployment and inspect the rollout status and the rollout history:
```shell
$ k create -f deployment-definition.yaml
deployment.apps/nginx created

$ k rollout status deployment/myapp-deployment
Waiting for deployment "nginx" rollout to finish: 0 of 1 updated replicas are available...
deployment "nginx" successfully rolled out

$ k rollout history deployment nginx
deployment.extensions/nginx
REVISION CHANGE-CAUSE
1     <none>
```

## Using the --revision flag
Here the revision 1 is the first version where the deployment was created.

You can check the status of each revision individually by using the --revision flag:

```shell
$ k rollout history deployment nginx --revision=1
deployment.extensions/nginx with revision #1
 
Pod Template:
 Labels:    app=nginx    pod-template-hash=6454457cdb
 Containers:  nginx:  Image:   nginx:1.16
  Port:    <none>
  Host Port: <none>
  Environment:    <none>
  Mounts:   <none>
 Volumes:   <none>
```

## Using the --record flag
You would have noticed that the "change-cause" field is empty in the rollout history output. We can use the --record flag to save the command used to create/update a deployment against the revision number.

```shell
$ kubectl set image deployment nginx nginx=nginx:1.17 --record
deployment.extensions/nginx image updated

$ kubectl rollout history deployment nginx
deployment.extensions/nginx
 
REVISION CHANGE-CAUSE
1     <none>
2     kubectl set image deployment nginx nginx=nginx:1.17 --record=true
```
You can now see that the change-cause is recorded for the revision 2 of this deployment.

Let's make some more changes. In the example below, we are editing the deployment and changing the image from `nginx:1.17` to `nginx:latest` while making use of the `--record` flag.

```shell
$ kubectl edit deployments nginx --record
deployment.extensions/nginx edited
 
$ kubectl rollout history deployment nginx
REVISION CHANGE-CAUSE
1     <none>
2     kubectl set image deployment nginx nginx=nginx:1.17 --record=true
3     kubectl edit deployments. nginx --record=true
 
 
 
$ kubectl rollout history deployment nginx --revision=3
deployment.extensions/nginx with revision #3
 
Pod Template: Labels:    app=nginx
    pod-template-hash=df6487dc Annotations: kubernetes.io/change-cause: kubectl edit deployments. nginx --record=true
 
 Containers:
  nginx:
  Image:   nginx:latest
  Port:    <none>
  Host Port: <none>
  Environment:    <none>
  Mounts:   <none>
  Volumes:   <none>
```

## Undo a change
Lets now rollback to the previous revision:

```shell
$ kubectl rollout history deployment nginx
deployment.apps/nginx 
REVISION  CHANGE-CAUSE
1         <none>
3         kubectl edit deployments.apps nginx --record=true
4         kubectl set image deployment nginx nginx=nginx:1.17 --record=true
 

$ kubectl rollout history deployment nginx --revision=3
deployment.apps/nginx with revision #3
Pod Template:
  Labels:       app=nginx
        pod-template-hash=787f54657b
  Annotations:  kubernetes.io/change-cause: kubectl edit deployments.apps nginx --record=true
  Containers:
   nginx:
    Image:      nginx:latest
    Port:      <none> 
    Host Port:  <none>
    Environment: <none>       
    Mounts:     <none>
  Volumes:      


$ kubectl describe deployments. nginx | grep -i image:
    Image:        nginx:1.17
```
With this, we have rolled back to the previous version of the deployment with the `image = nginx:1.17`.

```shell
$ kubectl rollout history deployment nginx --revision=1
deployment.apps/nginx with revision #1
Pod Template:
  Labels:       app=nginx
        pod-template-hash=78449c65d4
  Containers:
   nginx:
    Image:      nginx:1.16
    Port:       <none> 
    Host Port:  <none>
    Environment: <none>     
    Mounts:     <none>
  Volumes:      
 
$ kubectl rollout undo deployment nginx --to-revision=1
deployment.apps/nginx rolled back
```

To rollback to specific revision we will use the `--to-revision` flag.
With `--to-revision=1`, it will be rolled back with the first image we used to create a deployment as we can see in the rollout history output.

```shell
$ kubectl describe deployments. nginx | grep -i image:
Image: nginx:1.16
```