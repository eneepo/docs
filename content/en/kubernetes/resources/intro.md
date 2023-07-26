---
title: "Intro"
description: "A list of all the supported resource types and their abbreviated aliases in Kubernetes"
date: 2023-07-09T16:48:37+02:00
lastmod: 2023-07-09T16:48:37+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resources"
weight: 1000
toc: true
---
## kind & apiVersion
The following table includes a list of all the supported resource types and their abbreviated aliases.

(This output can be retrieved from kubectl api-resources, and was accurate as of Kubernetes 1.25.0)

| NAME                            | SHORTNAMES                           | APIVERSION              | NAMESPACED                     | KIND                      |
|---------------------------------|--------------------------------------|-------------------------|--------------------------------|---------------------------|
| bindings                        | v1                                   | true                    | Binding                        |                           |
| componentstatuses               | cs                                   | v1                      | false                          | ComponentStatus           |
| configmaps                      | cm                                   | v1                      | true                           | ConfigMap                 |
| endpoints                       | ep                                   | v1                      | true                           | Endpoints                 |
| events                          | ev                                   | v1                      | true                           | Event                     |
| limitranges                     | limits                               | v1                      | true                           | LimitRange                |
| namespaces                      | ns                                   | v1                      | false                          | Namespace                 |
| nodes                           | no                                   | v1                      | false                          | Node                      |
| persistentvolumeclaims          | pvc                                  | v1                      | true                           | PersistentVolumeClaim     |
| persistentvolumes               | pv                                   | v1                      | false                          | PersistentVolume          |
| pods                            | po                                   | v1                      | true                           | Pod                       |
| podtemplates                    | v1                                   | true                    | PodTemplate                    |                           |
| replicationcontrollers          | rc                                   | v1                      | true                           | ReplicationController     |
| resourcequotas                  | quota                                | v1                      | true                           | ResourceQuota             |
| secrets                         | v1                                   | true                    | Secret                         |                           |
| serviceaccounts                 | sa                                   | v1                      | true                           | ServiceAccount            |
| services                        | svc                                  | v1                      | true                           | Service                   |
| mutatingwebhookconfigurations   | admissionregistration.k8s.io/v1      | false                   | MutatingWebhookConfiguration   |                           |
| validatingwebhookconfigurations | admissionregistration.k8s.io/v1      | false                   | ValidatingWebhookConfiguration |                           |
| customresourcedefinitions       | crd,crds                             | apiextensions.k8s.io/v1 | false                          | CustomResourceDefinition  |
| apiservices                     | apiregistration.k8s.io/v1            | false                   | APIService                     |                           |
| controllerrevisions             | apps/v1                              | true                    | ControllerRevision             |                           |
| daemonsets                      | ds                                   | apps/v1                 | true                           | DaemonSet                 |
| deployments                     | deploy                               | apps/v1                 | true                           | Deployment                |
| replicasets                     | rs                                   | apps/v1                 | true                           | ReplicaSet                |
| statefulsets                    | sts                                  | apps/v1                 | true                           | StatefulSet               |
| tokenreviews                    | authentication.k8s.io/v1             | false                   | TokenReview                    |                           |
| localsubjectaccessreviews       | authorization.k8s.io/v1              | true                    | LocalSubjectAccessReview       |                           |
| selfsubjectaccessreviews        | authorization.k8s.io/v1              | false                   | SelfSubjectAccessReview        |                           |
| selfsubjectrulesreviews         | authorization.k8s.io/v1              | false                   | SelfSubjectRulesReview         |                           |
| subjectaccessreviews            | authorization.k8s.io/v1              | false                   | SubjectAccessReview            |                           |
| horizontalpodautoscalers        | hpa                                  | autoscaling/v2          | true                           | HorizontalPodAutoscaler   |
| cronjobs                        | cj                                   | batch/v1                | true                           | CronJob                   |
| jobs                            | batch/v1                             | true                    | Job                            |                           |
| certificatesigningrequests      | csr                                  | certificates.k8s.io/v1  | false                          | CertificateSigningRequest |
| leases                          | coordination.k8s.io/v1               | true                    | Lease                          |                           |
| endpointslices                  | discovery.k8s.io/v1                  | true                    | EndpointSlice                  |                           |
| events                          | ev                                   | events.k8s.io/v1        | true                           | Event                     |
| flowschemas                     | flowcontrol.apiserver.k8s.io/v1beta2 | false                   | FlowSchema                     |                           |
| prioritylevelconfigurations     | flowcontrol.apiserver.k8s.io/v1beta2 | false                   | PriorityLevelConfiguration     |                           |
| ingressclasses                  | networking.k8s.io/v1                 | false                   | IngressClass                   |                           |
| ingresses                       | ing                                  | networking.k8s.io/v1    | true                           | Ingress                   |
| networkpolicies                 | netpol                               | networking.k8s.io/v1    | true                           | NetworkPolicy             |
| runtimeclasses                  | node.k8s.io/v1                       | false                   | RuntimeClass                   |                           |
| poddisruptionbudgets            | pdb                                  | policy/v1               | true                           | PodDisruptionBudget       |
| clusterrolebindings             | rbac.authorization.k8s.io/v1         | false                   | ClusterRoleBinding             |                           |
| clusterroles                    | rbac.authorization.k8s.io/v1         | false                   | ClusterRole                    |                           |
| rolebindings                    | rbac.authorization.k8s.io/v1         | true                    | RoleBinding                    |                           |
| roles                           | rbac.authorization.k8s.io/v1         | true                    | Role                           |                           |
| priorityclasses                 | pc                                   | scheduling.k8s.io/v1    | false                          | PriorityClass             |
| csidrivers                      | storage.k8s.io/v1                    | false                   | CSIDriver                      |                           |
| csinodes                        | storage.k8s.io/v1                    | false                   | CSINode                        |                           |
| csistoragecapacities            | storage.k8s.io/v1                    | true                    | CSIStorageCapacity             |                           |
| storageclasses                  | sc                                   | storage.k8s.io/v1       | false                          | StorageClass              |
| volumeattachments               | storage.k8s.io/v1                    | false                   | VolumeAttachment               |                           |
{.table-sm}


```shell
kubectl api-resources
```

## apiVersion
What does each apiVersion mean?
### alpha
API versions with ‘alpha’ in their name are early candidates for new functionality coming into Kubernetes. These may contain bugs and are not guaranteed to work in the future.

### beta
‘beta’ in the API version name means that testing has progressed past alpha level, and that the feature will eventually be included in Kubernetes. Although the way it works might change, and the way objects are defined may change completely, the feature itself is highly likely to make it into Kubernetes in some form.

### stable
These do not contain ‘alpha’ or ‘beta’ in their name. They are safe to use.

---


### v1
This was the first stable release of the Kubernetes API. It contains many core objects.

### apps/v1
apps is the most common API group in Kubernetes, with many core objects being drawn from it and v1. It includes functionality related to running applications on Kubernetes, like Deployments, RollingUpdates, and ReplicaSets.

### autoscaling/v1
This API version allows pods to be autoscaled based on different resource usage metrics. This stable version includes support for only CPU scaling, but future alpha and beta versions will allow you to scale based on memory usage and custom metrics.

### batch/v1
The batch API group contains objects related to batch processing and job-like tasks (rather than application-like tasks like running a webserver indefinitely). This apiVersion is the first stable release of these API objects.

### batch/v1beta1
A beta release of new functionality for batch objects in Kubernetes, notably including CronJobs that let you run Jobs at a specific time or periodicity.

### certificates.k8s.io/v1beta1
This API release adds functionality to validate network certificates for secure communication in your cluster. You can read more on [the official docs](https://kubernetes.io/docs/tasks/tls/managing-tls-in-a-cluster/).

### extensions/v1beta1
This version of the API includes many new, commonly used features of Kubernetes. Deployments, DaemonSets, ReplicaSets, and Ingresses all received significant changes in this release.

Note that in Kubernetes 1.6, some of these objects were relocated from extensions to specific API groups (e.g. apps). When these objects move out of beta, expect them to be in a specific API group like apps/v1. Using extensions/v1beta1 is becoming deprecated—try to use the specific API group where possible, depending on your Kubernetes cluster version.

### policy/v1beta1
This apiVersion adds the ability to set a pod disruption budget and new rules around pod security.

### rbac.authorization.k8s.io/v1
This apiVersion includes extra functionality for Kubernetes role-based access control. This helps you to secure your cluster.



