---
title: "Adission Controllers"
description: ""
date: 2023-08-20T17:34:41Z
lastmod: 2023-08-20T17:34:41Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "auth"
weight: 1230
toc: true
---

Admission controllers are a crucial part of the API server's request processing pipeline. They are responsible for intercepting and potentially modifying requests to the Kubernetes API server before they are persisted to the etcd data store or acted upon by the server. Admission controllers help enforce security, governance, and policy rules on the resources being created or modified within a cluster.

## Admission Controllers Types

In Kubernetes, there are several built-in mutating and validating admission controllers, each serving a specific purpose in the cluster's request processing pipeline. Here is a list of some common mutating and validating admission controllers:

### Mutating Admission Controllers

1. **AlwaysPullImages**: This controller ensures that pods have their containers' images explicitly defined. If not, it automatically adds the image pull policy to "Always."

2. **DefaultStorageClass**: It sets the default storage class for dynamic provisioning of persistent volumes.

3. **NamespaceAutoProvisioning**: Automatically provisions namespaces if they don't already exist.

4. **LimitPodHardAntiAffinityTopology**: This controller ensures that pods with hard anti-affinity rules have a specific topology key set. It is used to enhance high availability.

5. **NodeRestriction**: This controller restricts access to `kubectl proxy` and `kubectl port-forward` to users with administrative privileges.

6. **PodNodeSelector**: Automatically adds a node selector to pods that don't have one, ensuring they are scheduled on nodes with specific labels.

7. **ServiceAccount**: Ensures that a ServiceAccount is defined for pods.

### Validating Admission Controllers

1. **AlwaysDeny**: Denies all requests to the API server, regardless of the request type or user identity.

2. **AlwaysPullImages**: Validates that containers within pods have a defined image pull policy. If not, it rejects the request.

3. **DenyEscalatingExec**: Prevents users from escalating pod exec requests to containers with more privileges than the requester.

4. **NamespaceLifecycle**: Enforces controls on namespace lifecycle, such as preventing the deletion of non-empty namespaces.

5. **ResourceQuota**: Enforces resource usage quotas for namespaces, ensuring that resource limits are not exceeded.

6. **PodSecurityPolicy**: Enforces pod security policies based on defined constraints, ensuring pods adhere to security standards.

7. **ServiceAccount**: Validates that the ServiceAccount specified in a pod's manifest exists in the namespace.

8. **ValidatingAdmissionWebhook**: Allows integration with external webhook services for custom validation of admission requests.

9. **MutatingAdmissionWebhook**: Allows integration with external webhook services for custom mutation of admission requests.

10. **ValidatingIPBlock**: Validates IP block fields in NetworkPolicy resources to ensure they are correctly formatted.

11. **ExtendedResourceToleration**: Extends resource toleration logic for pods to include non-standard resource types.

12. **PodTolerationRestriction**: Ensures that pods with certain tolerations cannot be scheduled on specific nodes.

Admission controllers are configured in the Kubernetes API server's admission-control flag or field of the kube-apiserver configuration file. You can enable, disable, or reorder admission controllers to meet your cluster's specific requirements.

## Enable/Disable Admission Plugins
The `--enable-admission-plugins` and `--disable-admission-plugins` flags or configuration settings for the API server are used to control which admission controllers are active in the cluster. Admission controllers intercept and validate or mutate requests to the Kubernetes API server before those requests are persisted to etcd or executed. These flags allow cluster administrators to customize the set of admission controllers to meet specific policy and security requirements.

Here's an explanation of both flags:

1. **--enable-admission-plugins**: This flag is used to specify a list of admission controllers that should be enabled and active in the Kubernetes cluster. When you use this flag, you are explicitly enabling a specific set of admission controllers, which means that only the controllers you specify will be active, and all others will be disabled. The admission controllers are listed in a comma-separated format.

   Example:
   ```
   --enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,ValidatingAdmissionWebhook,MutatingAdmissionWebhook
   ```

   In this example, the specified admission controllers will be active, and any others not listed here will be disabled.

2. **--disable-admission-plugins**: Conversely, this flag is used to specify a list of admission controllers that should be disabled or inactive in the cluster. When you use this flag, you are explicitly disabling a specific set of admission controllers, meaning that all admission controllers except those specified will be active.

   Example:
   ```
   --disable-admission-plugins=AlwaysAdmission,ServiceAccount
   ```

   In this example, all admission controllers except `AlwaysAdmission` and `ServiceAccount` will be active.

### View Enabled Admission Controllers
It's important to note that not all admission controllers can be enabled or disabled using these flags. Some admission controllers are always active, and others are optional. The availability of admission controllers may also vary depending on your Kubernetes version.

To see enabled admission controllers
```bash
kube-apiserver -h | grep enable-admission-plugins
kubectl exec -it pod/kube-apiserver-docker-desktop -n kube-system -- kube-apiserver -h | grep enable-admission-plugins
```

To see what is enabled that wasn't enabled by default:
```bash
grep enable-admission-plugins /etc/kubernetes/manifests/kube-apiserver.yaml
    - --enable-admission-plugins=NodeRestriction
```

If the kube-apiserver is running as pod you can check the process to see enabled and disabled plugins
```bash
ps -ef | grep kube-apiserver | grep admission-plugins
```

## Webhooks

Mutating Admission Webhooks and Validating Admission Webhooks are mechanisms that allow you to integrate external services or custom logic into the admission control process. These webhooks are part of the Kubernetes Admission Control system and enable you to modify (mutate) or validate admission requests to the Kubernetes API server before they are persisted or acted upon.

Here's a description of both types of webhooks with examples:

### Mutating Admission Webhook:

**Purpose**: Mutating Admission Webhooks are used to modify the content of admission requests before they are stored in etcd or processed by the Kubernetes API server. This can be useful for tasks like injecting sidecar containers, setting default values, or ensuring specific configurations.

**How It Works**:
1. When an admission request is made to the Kubernetes API server, the request is first sent to the Mutating Admission Webhook.
2. The webhook service processes the request and can make changes to the request object, such as adding or modifying fields.
3. The modified request is then passed to the rest of the admission control process.
4. If the request passes all other checks and validations, it is persisted or executed with the modifications made by the webhook.

**Example**: Let's say you want to add a sidecar container to every pod that doesn't already have one. You can create a Mutating Admission Webhook that automatically injects the sidecar container.

### Validating Admission Webhook:

**Purpose**: Validating Admission Webhooks are used to validate whether an admission request should be accepted or rejected based on specific criteria or policies. They are typically used for enforcing security, governance, and policy checks.

**How It Works**:
1. When an admission request is made to the Kubernetes API server, the request is first sent to the Validating Admission Webhook.
2. The webhook service processes the request and validates it against predefined policies or custom rules.
3. If the request meets the criteria, it is allowed to proceed to the next stage of processing.
4. If the request fails validation, it is rejected, and the API server returns an error to the client.

**Example**: Suppose you want to enforce a policy that requires all pods to have specific labels for security reasons. You can create a Validating Admission Webhook that checks for the presence of these labels and rejects any pod creation request that doesn't comply.

### Create a Custom Admission Webhook

Creating a custom Validating Admission Webhook for Kubernetes is a multi-step process that involves developing a webhook service, setting up TLS encryption, and registering the webhook with the Kubernetes API server. Below, I'll provide a simplified example using Python and Flask to demonstrate how to create a custom Validating Admission Webhook.

1. **Create a Python Flask Webhook Service**:

   Here's a simple Python script using Flask to create a Validating Admission Webhook. This example checks if a Pod has specific labels defined in the `metadata.labels` field.

   ```python
   from flask import Flask, request, jsonify
   import json

   app = Flask(__name__)

   @app.route('/validate', methods=['POST'])
   def validate_pod():
       admission_review = json.loads(request.data.decode('utf-8'))
       admission_request = admission_review['request']

       # Check for required labels
       required_labels = {"app": "my-app", "env": "production"}
       pod_labels = admission_request['object']['metadata']['labels']

       for label, value in required_labels.items():
           if pod_labels.get(label) != value:
               response = {
                   "apiVersion": "admission.k8s.io/v1",
                   "kind": "AdmissionReview",
                   "response": {
                       "allowed": False,
                       "status": {
                           "reason": f"Pod must have label {label} with value {value}"
                       }
                   }
               }
               return jsonify(response)

       # If all checks pass, allow the request
       response = {
           "apiVersion": "admission.k8s.io/v1",
           "kind": "AdmissionReview",
           "response": {
               "allowed": True
           }
       }
       return jsonify(response)

   if __name__ == '__main__':
       app.run(host='0.0.0.0', port=443, ssl_context=('cert.pem', 'key.pem'))
   ```

2. **Generate TLS Certificates**:

   You'll need TLS certificates (cert.pem and key.pem) to secure the communication between your webhook service and the Kubernetes API server. You can use tools like OpenSSL to generate self-signed certificates for development purposes.

3. **Deploy and Expose the Webhook Service**:

   Deploy your webhook service in your Kubernetes cluster and expose it via a service of type `ClusterIP` or `LoadBalancer`, depending on your network setup.

4. **Configure the Kubernetes API Server**:

   Modify the Kubernetes API server configuration to include the webhook URL in the `--admission-control` flag. You need to add the `ValidatingAdmissionWebhook` admission controller with the appropriate configuration.

   ```yaml
   --admission-control=...,ValidatingAdmissionWebhook
   --validating-admission-webhook-config-file=/path/to/your/webhook-config.yaml
   --validating-admission-webhook-config-file=/path/to/your/webhook-config-2.yaml
   ```

5. **Create a Webhook Configuration**:

   Create a Kubernetes webhook configuration (webhook-config.yaml) to specify the URL and other details of your webhook service. Here's an example:

   ```yaml
   apiVersion: admissionregistration.k8s.io/v1
   kind: ValidatingWebhookConfiguration
   metadata:
     name: my-webhook-config
   webhooks:
     - name: my-webhook.example.com
       clientConfig:
         url: https://webhook-service-name.default.svc:443/validate
         caBundle: "YOUR_BASE64_ENCODED_CA_BUNDLE"
       rules:
       - operations: ["CREATE", "UPDATE"]
         apiGroups: [""]
         apiVersions: ["v1"]
         resources: ["pods"]
   ```

6. **Apply the Configuration**:

   Apply the webhook configuration using `kubectl apply -f webhook-config.yaml`.

7. **Test Your Webhook**:

   Create or update a Pod in your cluster to trigger the admission webhook. The webhook should validate the Pod's labels and reject the request if they don't meet the required criteria.

8. **Monitor Logs and Errors**:

   Monitor the logs of your webhook service for any errors or issues during admission request handling.

Remember that this is a simplified example, and a production-ready webhook should include proper error handling, authentication, and security measures. Additionally, you may need to consider scaling and high availability for the webhook service in a production environment.