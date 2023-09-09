---
title: "Templating Language"
description: ""
date: 2023-09-06T20:06:54Z
lastmod: 2023-09-06T20:06:54Z
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "extra"
weight: 9952
toc: true
---

Helm uses a templating language called Go templates to dynamically generate Kubernetes manifests based on user-defined values and templates. This page will provide you with an introduction to Helm's templating language and its core concepts:

## Basic Syntax

Helm templates use double curly braces `{{ }}` to enclose template expressions. For example:

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: {{ .Release.Name }}-service
```

In this example, `.Release.Name` is a Helm built-in variable that refers to the release name.

## Variables and Values

Helm templates work with values defined in the `values.yaml` file or overridden during chart installation. You can access these values using the `.Values` object. For instance:

```yaml
image:
  repository: {{ .Values.image.repository }}
  tag: {{ .Values.image.tag }}
```

## Control Structures

Helm templates support basic control structures, such as `if`, `range`, and `with`, which allow you to conditionally include or loop through sections of your templates.

**If Statements:**

```yaml
{{ if .Values.enableDebug }}
  # Include debug configuration
{{ else }}
  # Include regular configuration
{{ end }}
```

**Range Loops:**

```yaml
{{ range .Values.servers }}
  - name: {{ .name }}
    port: {{ .port }}
{{ end }}
```

**With Statements:**

```yaml
{{ with .Values.database }}
  host: {{ .host }}
  port: {{ .port }}
{{ end }}
```

## Functions and Pipelines

Helm provides several [built-in functions](https://helm.sh/docs/chart_template_guide/functions_and_pipelines/) that allow you to manipulate data and perform operations within your templates. For example:

```yaml
# Concatenate strings
fullname: {{ printf "%s-%s" .Release.Name .Release.Namespace }}

# Get the length of a list
replicaCount: {{ len .Values.replicaCount }}

# Convert a string to uppercase
uppercaseName: {{ upper .Values.name }}
```

You can chain functions together using pipelines, represented by `|`. For instance:

```yaml
{{ .Values.name | upper | trimAll " " }}
```

## Default Values

Helm allows you to set default values using the `default` function. This function will use the specified default value if the variable is not defined or is set to `nil`.

```yaml
replicaCount: {{ .Values.replicaCount | default 1 }}
```

## Comments

You can add comments in Helm templates using the `/* ... */` syntax for multi-line comments or `{{/* ... */}}` for single-line comments.

```yaml
{{/* This is a single-line comment */}}

{{/*
  This is a
  multi-line comment
*/}}
```

## Whitespace Control

Helm templates automatically trim leading and trailing whitespace. To control whitespace explicitly, you can use the `{{-` and `-}}` operators:

```yaml
{{- if .Values.enabled -}}
  # Some content
{{- end -}}
```

This will remove any whitespace before or after the `if` block.

Certainly, here are a few more tips and advanced topics related to Helm's templating language:

## Accessing Built-in Objects

In addition to `.Values` and `.Release`, Helm provides several built-in objects that you can access in your templates. These include:

- `.Chart` for information about the chart itself.
- `.Files` for accessing files within the chart.
- `.Capabilities` for information about the Kubernetes cluster's capabilities.

For example, you can access the chart's version and description using `.Chart`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  chartVersion: {{ .Chart.Version }}
  chartDescription: {{ .Chart.Description }}
```

## Partial Templates

Helm allows you to create reusable template fragments called "partials." You can define these partials in separate files within the `templates` directory and then include them in other templates using the `include` function.

For example, you can create a `_helpers.tpl` partial to define common variables and functions and then include it in your other templates:

```yaml
{{- include "my-chart._helpers.tpl" . | nindent 2 }}
```

## Using Helm's `tpl` Function

You can use the `tpl` function to render a template expression and capture the result in a variable. This can be useful for creating more complex templates or for reusing rendered template values.

```yaml
{{- $formattedName := tpl "{{ .Values.firstName }} {{ .Values.lastName }}" . }}
```

## Helm Sprig Library

Helm uses the Sprig library to provide a wide range of additional functions beyond the built-in ones. Sprig functions can be very helpful for manipulating data, working with strings, and more. You can find the full list of Sprig functions in the Helm documentation.

## Custom Functions and Helpers

Helm allows you to define your own custom functions and helpers in Go that you can use in your templates. This can be valuable when you need to implement custom logic or calculations specific to your application.


## Testing and Debugging

You can test and debug your Helm templates by using the `helm template` command. This command renders your templates and displays the resulting Kubernetes manifests without installing them in a cluster.

```bash
helm template my-release ./my-chart
```

## Best Practices

- Keep your templates simple and readable.
- Use functions and conditionals judiciously to avoid complex logic in templates.
- Document your templates and values.yaml file for clarity.
- Validate your templates using the `helm lint` command.
