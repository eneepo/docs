---
title: "Network Policy"
description: ""
date: 2023-07-30T18:03:07Z
lastmod: 2023-07-30T18:03:07Z
draft: false
images: []
menu:
  docs:
    parent: "networking"
type: docs
weight: 1040
toc: true
---

NetworkPolicies are used to control the network traffic between different pods in a cluster. They allow you to define rules for allowing or denying incoming **Ingress** and outgoing **Egress** network traffic to and from pods that match certain criteria. NetworkPolicies are implemented using labels and selectors to target specific pods.

## Ingress
Ingress network policies control the incoming network traffic to pods. They define the rules for allowing or denying traffic from other pods or external sources into the selected pods. Ingress rules are evaluated for incoming traffic to the pods that match the NetworkPolicy's pod selector.

Here's an example of an Ingress NetworkPolicy YAML:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-ingress-from-specific-namespaces
spec:
  podSelector:
    # Define the pods that this NetworkPolicy applies to.
    matchLabels:
      app: my-app
  policyTypes:
    - Ingress
  ingress:
    - from:
        # Allow traffic from pods with "name: app-pod" label in dev namespace
        - namespaceSelector:
            matchLabels:
              environment: dev
          podSelector:
            matchLabels:
              name: app-pod
        # Allow traffic from all pods in stg namespace
        - namespaceSelector:
            matchLabels:
              environment: stg
        # Allow traffic from this ip address
        - ipBlock:
            cidr: 192.168.0.50/32
      # Define the ports that are allowed for ingress.
      - ports:
          - protocol: TCP
            port: 80
          - protocol: TCP
            port: 443

```


## Egress
Egress network policies control the outgoing network traffic from pods. They define the rules for allowing or denying traffic from the selected pods to other pods or external destinations. Egress rules are evaluated for outgoing traffic from the pods that match the NetworkPolicy's pod selector.

Here's an example of an Egress NetworkPolicy YAML:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-external-egress
spec:
  # Define the selector for the pods to which this NetworkPolicy applies.
  podSelector:
    matchLabels:
      # Select pods with the label "app: my-app".
      app: my-app
  policyTypes:
    - Egress
  egress:
    - to:
        # Allow egress traffic to pods with the label "role: backend".
        - podSelector:
            matchLabels:
              role: backend
        # Also, allow egress traffic to the IP block "10.0.0.0/16".
        - ipBlock:
            cidr: 10.0.0.0/16
```
