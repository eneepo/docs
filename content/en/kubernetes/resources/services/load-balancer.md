---
title: "Load Balancer"
description: ""
date: 2023-08-26T15:13:06Z
lastmod: 2023-08-26T15:13:06Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "networking"
weight: 1115
toc: true
---

A LoadBalancer service is a type of service that provides external network access to a set of pods within the cluster. It acts as a load balancer for distributing incoming network traffic across multiple pod instances of a specific service. This is particularly useful for ensuring high availability and scalability of applications running in Kubernetes.

The LoadBalancer service typically gets assigned an external IP address by the cloud provider (if you're using a cloud-based Kubernetes service like AWS, GCP, Azure, etc.). This external IP address is used by external clients to access the service.

Here's a simple example of a LoadBalancer service YAML configuration:

```yaml
# kubectl create service loadbalancer my-lb-service --tcp=80:8080 --dry-run=client -o yaml >

apiVersion: v1              # Specifies the Kubernetes API version being used for this resource.
kind: Service               # Specifies the type of Kubernetes resource, in this case, a Service.

metadata:
  creationTimestamp: null     # Placeholder for the creation timestamp; will be automatically set when the resource is created.

  labels:
    app: my-lb-service        # Labels to identify and categorize this service as related to 'my-lb-service'.

  name: my-lb-service         # Name of the service resource.

spec:
  ports:
  - name: 80-8080             # A user-friendly name for the port configuration.
    port: 80                  # The port number on the service.
    protocol: TCP             # The protocol used for communication, in this case, TCP.
    targetPort: 8080          # The port to forward incoming traffic to on the pods.

  selector:
    app: my-app               # The label selector to determine which pods will be included in this service.

  type: LoadBalancer          # The type of service; 'LoadBalancer' exposes the service externally using a cloud provider's load balancer.

status:
  loadBalancer: {}            # Placeholder for the status of the load balancer; will be updated as the service gets exposed.

```

When you create this service, Kubernetes will interact with the cloud provider's load balancer service (e.g., AWS Elastic Load Balancer or Azure Load Balancer) to provision an external IP and configure it to route traffic to the selected pods. 

This Service resource that exposes a network service using a LoadBalancer. The service will forward incoming traffic from port 80 to the pods' port 8080. The pods included in the service are selected based on the 'app: my-app' label. The 'labels' section is used for categorization and identification. The 'creationTimestamp' will be automatically set upon creation, and the 'status' section will hold information about the load balancer's status once the service is exposed.