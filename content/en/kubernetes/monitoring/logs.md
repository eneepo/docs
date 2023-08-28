---
title: "Logs"
description: ""
date: 2023-07-26T07:34:43+02:00
lastmod: 2023-07-26T07:34:43+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "monitoring"
weight: 1100
toc: true
---

`kubectl log` is a command-line tool in Kubernetes used for retrieving logs from containers running inside pods within a Kubernetes cluster.

## Example

### Single-container pod
```yaml
# log-simulator.yaml
apiVersion: v1
kind: Pod
metadata:
  name: log-simulator-pod
spec:
  containers:
  - image: mingrammer/flog
    name: log-simulator
```

```shell
k logs log-simulator-pod
```

### Multi-container P0od

```yaml
# multi-container-log-simulator.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mc-log-simulator-pod
spec:
  containers:
  - image: mingrammer/flog
    name: log-primary
  - image: mingrammer/flog
    name: log-secondary
```

```shell
k logs mc-log-simulator-pod log-secondary
```

### Streaming Logs

By default, `kubectl log` retrieves and displays the logs for a specific container from a given pod. You can also stream the logs in real-time by adding the `-f` or `--follow` flag. This is particularly useful for monitoring applications as they run.

```bash
kubectl logs -f <pod-name>
```

### Timestamps

To include timestamps in the log output, you can use the `--timestamps` flag. This helps you track when events occurred.

```bash
kubectl logs --timestamps <pod-name>
```

### Previous Container State

In cases where a container has terminated or encountered an issue, you can use `kubectl log` to retrieve logs from a previous instance of the container by adding the `--previous` flag.

```bash
kubectl logs --previous <pod-name>
```

### Tail Logs

To limit the number of lines displayed, you can use the `-n` or `--tail` flag to specify the number of lines to show from the end of the log.

```bash
kubectl logs --tail=<number-of-lines> <pod-name>
```

### Output Format

By default, `kubectl log` displays logs in plain text. However, you can specify other output formats such as JSON or YAML using the `-o` or `--output` flag.

```bash
kubectl logs -o json <pod-name>
```
