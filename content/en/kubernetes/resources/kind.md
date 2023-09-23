---
title: "kind"
description: "A list of all the supported resource types and their abbreviated aliases in Kubernetes"
date: 2023-07-09T16:48:37+02:00
lastmod: 2023-07-09T16:48:37+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resources"
weight: 990
toc: true
---
## kind & apiVersion
The following table includes a list of all the supported resource types and their abbreviated aliases.

(This output can be retrieved from kubectl api-resources, and was accurate as of Kubernetes v1.27.4-eks)

| NAME                            | SHORTNAMES | APIVERSION                           | NAMESPACED | KIND                           |
|---------------------------------|------------|--------------------------------------|------------|--------------------------------|
| bindings                        |            | v1                                   | TRUE       | Binding                        |
| componentstatuses               | cs         | v1                                   | FALSE      | ComponentStatus                |
| configmaps                      | cm         | v1                                   | TRUE       | ConfigMap                      |
| endpoints                       | ep         | v1                                   | TRUE       | Endpoints                      |
| events                          | ev         | v1                                   | TRUE       | Event                          |
| limitranges                     | limits     | v1                                   | TRUE       | LimitRange                     |
| namespaces                      | ns         | v1                                   | FALSE      | Namespace                      |
| nodes                           | no         | v1                                   | FALSE      | Node                           |
| persistentvolumeclaims          | pvc        | v1                                   | TRUE       | PersistentVolumeClaim          |
| persistentvolumes               | pv         | v1                                   | FALSE      | PersistentVolume               |
| pods                            | po         | v1                                   | TRUE       | Pod                            |
| podtemplates                    |            | v1                                   | TRUE       | PodTemplate                    |
| replicationcontrollers          | rc         | v1                                   | TRUE       | ReplicationController          |
| resourcequotas                  | quota      | v1                                   | TRUE       | ResourceQuota                  |
| secrets                         |            | v1                                   | TRUE       | Secret                         |
| serviceaccounts                 | sa         | v1                                   | TRUE       | ServiceAccount                 |
| services                        | svc        | v1                                   | TRUE       | Service                        |
| mutatingwebhookconfigurations   |            | admissionregistration.k8s.io/v1      | FALSE      | MutatingWebhookConfiguration   |
| validatingwebhookconfigurations |            | admissionregistration.k8s.io/v1      | FALSE      | ValidatingWebhookConfiguration |
| customresourcedefinitions       | crd,crds   | apiextensions.k8s.io/v1              | FALSE      | CustomResourceDefinition       |
| apiservices                     |            | apiregistration.k8s.io/v1            | FALSE      | APIService                     |
| controllerrevisions             |            | apps/v1                              | TRUE       | ControllerRevision             |
| daemonsets                      | ds         | apps/v1                              | TRUE       | DaemonSet                      |
| deployments                     | deploy     | apps/v1                              | TRUE       | Deployment                     |
| replicasets                     | rs         | apps/v1                              | TRUE       | ReplicaSet                     |
| statefulsets                    | sts        | apps/v1                              | TRUE       | StatefulSet                    |
| tokenreviews                    |            | authentication.k8s.io/v1             | FALSE      | TokenReview                    |
| localsubjectaccessreviews       |            | authorization.k8s.io/v1              | TRUE       | LocalSubjectAccessReview       |
| selfsubjectaccessreviews        |            | authorization.k8s.io/v1              | FALSE      | SelfSubjectAccessReview        |
| selfsubjectrulesreviews         |            | authorization.k8s.io/v1              | FALSE      | SelfSubjectRulesReview         |
| subjectaccessreviews            |            | authorization.k8s.io/v1              | FALSE      | SubjectAccessReview            |
| horizontalpodautoscalers        | hpa        | autoscaling/v2                       | TRUE       | HorizontalPodAutoscaler        |
| cronjobs                        | cj         | batch/v1                             | TRUE       | CronJob                        |
| jobs                            |            | batch/v1                             | TRUE       | Job                            |
| certificatesigningrequests      | csr        | certificates.k8s.io/v1               | FALSE      | CertificateSigningRequest      |
| leases                          |            | coordination.k8s.io/v1               | TRUE       | Lease                          |
| eniconfigs                      |            | crd.k8s.amazonaws.com/v1alpha1       | FALSE      | ENIConfig                      |
| endpointslices                  |            | discovery.k8s.io/v1                  | TRUE       | EndpointSlice                  |
| events                          | ev         | events.k8s.io/v1                     | TRUE       | Event                          |
| flowschemas                     |            | flowcontrol.apiserver.k8s.io/v1beta3 | FALSE      | FlowSchema                     |
| prioritylevelconfigurations     |            | flowcontrol.apiserver.k8s.io/v1beta3 | FALSE      | PriorityLevelConfiguration     |
| policyendpoints                 |            | networking.k8s.aws/v1alpha1          | TRUE       | PolicyEndpoint                 |
| ingressclasses                  |            | networking.k8s.io/v1                 | FALSE      | IngressClass                   |
| ingresses                       | ing        | networking.k8s.io/v1                 | TRUE       | Ingress                        |
| networkpolicies                 | netpol     | networking.k8s.io/v1                 | TRUE       | NetworkPolicy                  |
| runtimeclasses                  |            | node.k8s.io/v1                       | FALSE      | RuntimeClass                   |
| poddisruptionbudgets            | pdb        | policy/v1                            | TRUE       | PodDisruptionBudget            |
| clusterrolebindings             |            | rbac.authorization.k8s.io/v1         | FALSE      | ClusterRoleBinding             |
| clusterroles                    |            | rbac.authorization.k8s.io/v1         | FALSE      | ClusterRole                    |
| rolebindings                    |            | rbac.authorization.k8s.io/v1         | TRUE       | RoleBinding                    |
| roles                           |            | rbac.authorization.k8s.io/v1         | TRUE       | Role                           |
| priorityclasses                 | pc         | scheduling.k8s.io/v1                 | FALSE      | PriorityClass                  |
| csidrivers                      |            | storage.k8s.io/v1                    | FALSE      | CSIDriver                      |
| csinodes                        |            | storage.k8s.io/v1                    | FALSE      | CSINode                        |
| csistoragecapacities            |            | storage.k8s.io/v1                    | TRUE       | CSIStorageCapacity             |
| storageclasses                  | sc         | storage.k8s.io/v1                    | FALSE      | StorageClass                   |
| volumeattachments               |            | storage.k8s.io/v1                    | FALSE      | VolumeAttachment               |
| cninodes                        | cnd        | vpcresources.k8s.aws/v1alpha1        | FALSE      | CNINode                        |
| securitygrouppolicies           | sgp        | vpcresources.k8s.aws/v1beta1         | TRUE       | SecurityGroupPolicy            |
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



