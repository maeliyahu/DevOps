# Helm Library Chart Guide

A Helm **library** is a reusable set of templates and functions that can be shared across multiple charts. Library charts do not deploy any Kubernetes resources but provide reusable logic, templates, and helper functions that can be included in other charts.

## Key Concepts

- **Helm Library Chart**: A chart that contains reusable Kubernetes YAML templates and functions but does not include Kubernetes objects like Deployments, Services, etc.
- **Usage**: A library chart can be used by other charts as a dependency but does not deploy resources itself.

## How to Create a Library Chart

### 1. Create a Helm Chart with the Library Type

A library chart is defined in the `Chart.yaml` with the `type: library` field. This indicates that the chart is a library and not a regular chart.

Example `Chart.yaml` for a library:

```yaml
apiVersion: v2
name: my-library
description: A Helm library to manage common templates
type: library
version: 0.1.0
```

### 2. Create Reusable Templates

In the `templates/` directory, you can create reusable templates or helper functions. These templates are meant to be used by other charts that depend on this library.

Example template in `templates/_helpers.tpl`:

```yaml
{{/*
A helper to define a common label structure.
*/}}
{{- define "my-library.labels" -}}
app.kubernetes.io/name: {{ .Chart.Name }}
app.kubernetes.io/instance: {{ .Release.Name }}
{{- end }}
```

The `define` function is used to create reusable templates or helper functions. These functions can then be called by other templates.

### 3. Using the Library in Other Charts

To use a library chart in another chart, you must include it as a dependency in the `Chart.yaml` file of the dependent chart.

Example `Chart.yaml` for a dependent chart:

```yaml
apiVersion: v2
name: my-app
description: An example app using a Helm library
version: 0.1.0
dependencies:
  - name: my-library
    version: 0.1.0
    repository: "file://../my-library" # Path to the local library chart
```

In the dependent chart, you can now use the templates or functions defined in the library. For example, if you want to use the `labels` function from the library:

Example usage in a template:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-deployment
  labels:
    {{- include "my-library.labels" . | nindent 4 }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ .Chart.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ .Chart.Name }}
    spec:
      containers:
        - name: my-app
          image: "nginx"
          ports:
            - containerPort: 80
```

In this example, the `labels` helper function from the `my-library` chart is included and applied to the `metadata.labels` field in a `Deployment` template.

### 4. Installing the Dependent Chart

Once the library is set up as a dependency, you can install the dependent chart, and Helm will automatically pull in the library chart and use its templates.

Run the following command to install the dependent chart:

```bash
helm install my-app ./my-app
```

Helm will manage the installation of both the dependent chart and its library dependency.

## Summary

- **Create a Library Chart**: Define reusable templates or helper functions in a chart with `type: library` in the `Chart.yaml`.
- **Use a Library Chart**: Reference the library chart as a dependency in the `Chart.yaml` of another chart and use the defined templates and helpers.
- **Install Dependent Chart**: Install the chart, and Helm will automatically pull in and use the library chart.

## Additional Resources

- [Helm Documentation on Libraries](https://helm.sh/docs/topics/chart_best_practices/#library-charts)
