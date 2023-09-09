---
title: "Intro"
description: ""
date: 2023-09-05T15:52:05Z
lastmod: 2023-09-05T15:52:05Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "extra"
weight: 9950
toc: true
---

Helm is a package manager for Kubernetes that allows you to define, install, and manage Kubernetes applications as reusable packages known as "charts." Charts encapsulate all the necessary Kubernetes resources and configurations needed to deploy an application.

## Why use Helm?

Helm simplifies the deployment process for Kubernetes applications in the following ways:
- **Reusability**: Charts can be shared and reused across different projects and organizations.
- **Versioning**: Helm allows versioning of charts, making it easy to track changes and updates.
- **Templating**: Helm uses Go templating to customize configurations, making it flexible and dynamic.
- **Dependency Management**: Helm can manage dependencies between charts.
- **Rollbacks**: Helm provides an easy way to rollback to previous releases.
- **Community Support**: A thriving community creates and shares Helm charts for various applications.

## Concepts

### Charts

A Helm chart is a package containing all the Kubernetes resource definitions and configurations required to run an application in a Kubernetes cluster. Charts are organized with a predefined structure, including templates, values, and optional files.

### Releases

A release is an instance of a chart deployed in a Kubernetes cluster. Helm keeps track of releases, allowing you to manage them independently. Each release can have specific configurations and be upgraded or rolled back as needed.

### Helm Repository

Helm charts are stored in repositories, which can be public or private. Helm uses these repositories to search for and fetch charts during installation. You can also create your own Helm repository to share charts within your organization.

## Installation

Before you install Helm, ensure you have the following prerequisites:
- A Kubernetes cluster up and running.
- `kubectl` command-line tool installed and configured to communicate with your cluster.

### Manual insallation

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

If you don't have openssl installed you may want to use the following command to disable checksum check:
```bash
VERIFY_CHECKSUM=false ./get_helm.sh
```


### Brew

```bash
brew install helm
```

### Other methods

For more installation methods visit the [official installation doc](https://helm.sh/docs/intro/install/)

## Helm Repositories

Helm uses repositories to store and distribute charts, which are packages of pre-configured Kubernetes resources. 

### Adding a Helm Repository

To add a Helm repository, you need to use the helm repo add command, specifying a name for the repository and the URL where the repository is hosted. 

```bash
# General syntax to add Helm repository
helm repo add <repository-name> <repository-url>

# Add the official Helm stable charts repository
helm repo add stable https://charts.helm.sh/stable

# Add the prometheus-community respository
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

### View Helm Repositories

After adding the repository, Helm will update its local cache to retrieve the available charts. You can verify that the repository has been added by running:

```bash
# View the added repositories
$ helm repo list
NAME                    URL                                               
stable                  https://charts.helm.sh/stable                     
prometheus-community    https://prometheus-community.github.io/helm-charts
```

### Updating a Helm Repository

Running the following command will refresh the local cache of all added repositories, making sure you have the latest information about available charts and their versions.

```bash
helm repo update
```

### Searching for Helm Charts

Once you have added a Helm repository and updated its cache, you can search for available charts using the helm search repo command. For example:

```bash
helm search repo <chart-name>
```

## Helm Packages

### Downlaoding a Helm package

```bash
# Download the prometheus package from prometheus-community repositories
$ helm fetch prometheus-community/prometheus
$ ls
prometheus-24.3.0.tgz

# Extract the downloaded file package
$ tar -xzf ./prometheus-24.3.0.tgz 
$ ls ./prometheus/
Chart.lock  charts  Chart.yaml  README.md  templates  values.schema.json  values.yaml
```

## Commands

```bash
# Create a new basic Helm chart.
helm create <chart-name>

# Install a Helm chart.
helm install <release-name> <chart>

# Upgrade an existing release.
helm upgrade <release-name> <chart>

# List all installed releases.
helm list

# Delete a release.
helm uninstall <release-name>

helm version
helm repo add stable https://charts.helm.sh/stable
helm install demo ../demo --dry-run
helm show values ./chart/
```

## Resources

- [Helm Official Documentation](https://helm.sh/docs/)
- [Helm Charts Repository](https://hub.helm.sh/)
- [Helmfile Documentation](https://github.com/roboll/helmfile)
- [Kustomize Official Documentation](https://kustomize.io/)
- [Helm Operators Documentation](https://sdk.operatorframework.io/docs/building-operators/helm/tutorial/)
- [Helm Plugins](https://helm.sh/docs/topics/plugins/)
