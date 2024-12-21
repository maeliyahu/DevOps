# Helm Helpers Guide

Helm helpers are reusable template functions and partials defined in your Helm charts to simplify and streamline Kubernetes resource definitions. This guide explains how to use helpers effectively, provides examples, and demonstrates best practices.

---

## 1. What Are Helm Helpers?
Helm helpers are custom templates defined in the `templates/_helpers.tpl` file of a Helm chart. They are used to avoid repetition and to create consistent configurations across resources.

Helpers leverage the Go template language and can include:
- Variable substitutions
- Conditional logic
- Loops

### Benefits of Using Helpers:
- Code reusability
- Centralized management
- Improved readability

---

## 2. Defining Helpers
Helpers are defined as named templates within the `templates/_helpers.tpl` file.

### Example
```yaml
{{- define "my-chart.fullname" -}}
{{ .Release.Name }}-{{ .Chart.Name }}
{{- end -}}
```

In this example, the helper `my-chart.fullname` combines the release name and chart name into a single string.

---

## 3. Using Helpers
Helpers are invoked using the `template` function in other template files.

### Example Usage
```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ template "my-chart.fullname" . }}
spec:
  selector:
    app: {{ template "my-chart.fullname" . }}
```

This example uses the `my-chart.fullname` helper to ensure consistent naming for the Kubernetes Service and its selector.

---

## 4. Common Helper Patterns

### 4.1 Generating Labels
```yaml
{{- define "my-chart.labels" -}}
app.kubernetes.io/name: {{ .Chart.Name }}
app.kubernetes.io/instance: {{ .Release.Name }}
app.kubernetes.io/version: {{ .Chart.AppVersion }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{- end -}}
```

### Usage
```yaml
metadata:
  labels:
{{ include "my-chart.labels" . | indent 4 }}
```

### 4.2 Setting Default Values
```yaml
{{- define "my-chart.default-replicas" -}}
{{ default 3 .Values.replicaCount }}
{{- end -}}
```

### Usage
```yaml
spec:
  replicas: {{ template "my-chart.default-replicas" . }}
```

---

## 5. Best Practices

1. **Use Meaningful Names**:
   - Use descriptive names for helpers, prefixed with your chart name (e.g., `my-chart.fullname`).

2. **Centralize Logic**:
   - Move complex logic into helpers to improve readability in resource templates.

3. **Comment Your Helpers**:
   - Add comments to helpers to describe their purpose and usage.

4. **Reuse Standard Labels**:
   - Follow Kubernetes recommended labels for consistency.

---

## 6. Full Example

### `templates/_helpers.tpl`
```yaml
{{- define "my-chart.fullname" -}}
{{ .Release.Name }}-{{ .Chart.Name }}
{{- end -}}

{{- define "my-chart.labels" -}}
app.kubernetes.io/name: {{ .Chart.Name }}
app.kubernetes.io/instance: {{ .Release.Name }}
app.kubernetes.io/version: {{ .Chart.AppVersion }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{- end -}}
```

### `templates/deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ template "my-chart.fullname" . }}
  labels:
{{ include "my-chart.labels" . | indent 4 }}
spec:
  replicas: {{ default 3 .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ template "my-chart.fullname" . }}
  template:
    metadata:
      labels:
{{ include "my-chart.labels" . | indent 8 }}
    spec:
      containers:
        - name: app
          image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
```

---

## 7. Debugging Helpers

To debug helpers, use `helm template` to render your chart:
```bash
helm template my-release ./my-chart
```
This command outputs the rendered templates and allows you to verify helper logic.

---

Helm helpers are a powerful feature that enhances the flexibility and maintainability of your Helm charts. Use them to centralize and simplify your Kubernetes configurations!
