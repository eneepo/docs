---
title: "Env"
description: ""
date: 2023-07-18T08:16:47+02:00
lastmod: 2023-07-18T08:16:47+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "configuration"
weight: 1005
toc: true
---

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp-color
spec:
  containers:
  - name: simple-webapp-color
    image: simple-webapp-color
    ports:
      - containerPort: 8080
    env:
      - name: APP_COLOR
        value: pink
```

```shell
$ docker run -e APP_COLOR=pink simple-webapp-color
```

## ENV Value Types:
We can get environment variables in 3 different ways:

### 1. Plain Key Value
```yaml
env:
  - name: APP_COLOR
    value: pink
```
