---
title: "KubeConfig"
description: ""
date: 2023-08-19T14:28:28Z
lastmod: 2023-08-19T14:28:28Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "extra"
weight: 1110
toc: true
---

`kubeconfig` is a configuration file used by `kubectl`, the Kubernetes command-line tool, to interact with a Kubernetes cluster. It contains essential information and settings that `kubectl` needs to connect to a cluster, including the cluster's API server address, authentication credentials, and context.


## `kubeconfig` Breakdown
Here's a breakdown of the key elements and purposes of a `kubeconfig` file:

### Clusters
A `kubeconfig` file can specify multiple clusters. Each cluster entry contains information about a Kubernetes cluster, including the API server address, which is used to connect to the cluster.

### Credentials
For each cluster, there is an associated credentials section. This section includes the authentication method and the necessary credentials for accessing the cluster. Common authentication methods include:

- **Username and Password**: Basic authentication using a username and password.
- **Client Certificates**: Authentication using TLS client certificates.
- **Token**: Authentication using an authentication token.
- **Auth Provider**: Authentication through an external identity provider, like OIDC or GCP.

### Contexts
A `context` combines a cluster and credentials, allowing you to define a specific environment for `kubectl` to use. It specifies the cluster and user to use when executing commands. A `kubeconfig` file can have multiple contexts, making it easy to switch between different clusters or user identities.

### Current Context
The `current-context` field in the `kubeconfig` file indicates which context is currently active. When you run `kubectl` commands, they use the settings from the current context by default.

### User Preferences
A `kubeconfig` file can include additional preferences and settings, such as the default namespace to use when creating or interacting with resources.

Here's an example of a simplified `kubeconfig` file:

```yaml
---
apiVersion: v1
kind: Config
current-context: my-cluster-context

clusters:
  - name: my-cluster
    cluster:
      server: https://api.my-k8s-cluster.com
      certificate-authority: /path/to/ca.crt
  - name: another-cluster
    cluster:
      server: https://api.another-k8s-cluster.com
      certificate-authority: /path/to/another-ca.crt

users:
  - name: my-user
    user:
      client-certificate: /path/to/client.crt
      client-key: /path/to/client.key
  - name: another-user
    user:
      username: john
      password: secretpassword

contexts:
  - name: my-cluster-context
    context:
      cluster: my-cluster
      user: my-user
      namespace: my-namespace
  - name: another-cluster-context
    context:
      cluster: another-cluster
      user: another-user
```

In this example:

- There are two clusters (`my-cluster` and `another-cluster`), each with its API server address.
- Two users (`my-user` and `another-user`) with different authentication methods.
- Two contexts (`my-cluster-context` and `another-cluster-context`) that specify which cluster and user to use.
- `my-cluster-context` is set as the `current-context`, so when you run `kubectl` commands, it will use the settings defined in this context by default.
- The `namespace` field is added to context definition (e.g., `namespace: my-namespace`). This specifies the default namespace for `my-cluster-context` context.

To switch between contexts and clusters, you can use the `kubectl config use-context` command. For example, to switch to the `another-cluster-context`, you'd run:

```bash
kubectl config use-context another-cluster-context
```
## Converting `certificate-authority` to `certificate-authority-data`
You can specify the `certificate-authority` field either as a file path (`certificate-authority`) or as a base64-encoded certificate data (`certificate-authority-data`). If you have a CA certificate file and want to convert it to the `certificate-authority-data` format, you can do so using the `base64` command.

Here are the steps to convert a CA certificate file to `certificate-authority-data`:

1. **Encode the CA Certificate**:

   Use the `base64` command to encode the contents of your CA certificate file:

   ```bash
   cat /path/to/ca.crt | base64
   ```

   This will output the base64-encoded certificate data. Copy this encoded data.

2. **Update `kubeconfig`**:

   Update your `kubeconfig` file by replacing the `certificate-authority` field with `certificate-authority-data` and paste the base64-encoded data. Here's an example:

   ```yaml
   clusters:
   - name: my-cluster
     cluster:
       server: https://api.my-k8s-cluster.com
       certificate-authority-data: <base64-encoded-certificate-data>
   ```

   Replace `<base64-encoded-certificate-data>` with the actual base64-encoded certificate data you obtained in step 1.

3. **Save and Test**:

   Save your updated `kubeconfig` file. You can now use `kubectl` with this configuration, and it will use the `certificate-authority-data` for the cluster's certificate authority.

   ```bash
   kubectl get pods
   ```

This conversion allows you to keep the CA certificate directly within the `kubeconfig` file as base64-encoded data, which can be useful for cases where you don't want to rely on external certificate files.

## Cheatsheet
Here's a Kubernetes `kubectl` configuration cheat sheet that summarizes some of the most commonly used commands and operations for managing `kubeconfig` files:

### Viewing and Managing Configuration

1. **View the current context, cluster, and user:**
   ```bash
   kubectl config current-context
   ```

2. **List all available contexts:**
   ```bash
   kubectl config get-contexts
   ```

3. **Switch to a different context:**
   ```bash
   kubectl config use-context <context-name>
   ```

4. **Set the namespace for the current context:**
   ```bash
   kubectl config set-context --current --namespace=<namespace-name>
   ```

5. **Create a new context (combine an existing cluster and user):**
   ```bash
   kubectl config set-context <context-name> --cluster=<cluster-name> --user=<user-name>
   ```

### Cluster Configuration

6. **List all clusters defined in the `kubeconfig`:**
   ```bash
   kubectl config get-clusters
   ```

7. **Display detailed cluster information:**
   ```bash
   kubectl config view --minify --flatten -o jsonpath='{.clusters[0]}'
   ```

### User Configuration

8. **List all users defined in the `kubeconfig`:**
   ```bash
   kubectl config get-users
   ```

9. **Display detailed user information:**
   ```bash
   kubectl config view --minify --flatten -o jsonpath='{.users[0]}'
   ```

### Context Configuration

10. **List all available contexts:**
    ```bash
    kubectl config get-contexts
    ```

11. **Display detailed context information:**
    ```bash
    kubectl config view --minify --flatten -o jsonpath='{.contexts[0]}'
    ```

### Context and Cluster Switching

12. **Switch to a different context:**
    ```bash
    kubectl config use-context <context-name>
    ```

13. **Switch to a different cluster within the current context:**
    ```bash
    kubectl config set-context --current --cluster=<cluster-name>
    ```

14. **Switch to a different user within the current context:**
    ```bash
    kubectl config set-context --current --user=<user-name>
    ```

### Managing `kubeconfig` Files

15. **Specify a custom `kubeconfig` file location:**
    ```bash
    kubectl --kubeconfig=/path/to/custom/config.yaml <command>
    ```

16. **Merge two or more `kubeconfig` files into one:**
    ```bash
    KUBECONFIG=/path/to/first/config.yaml:/path/to/second/config.yaml kubectl config view --merge
    ```

17. **Set a `kubeconfig` file temporarily for a single command:**
    ```bash
    KUBECONFIG=/path/to/custom/config.yaml kubectl <command>
    ```

Remember to replace placeholders like `<context-name>`, `<cluster-name>`, `<user-name>`, and `<namespace-name>` with your actual configuration values. The `kubectl config` commands allow you to efficiently manage and switch between different Kubernetes clusters and contexts, making it easier to work with multiple environments.