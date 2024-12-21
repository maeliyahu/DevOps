# Helm Chart Dependencies Guide

This guide covers the fundamentals of managing chart dependencies in Helm, including how to define dependencies, pass values to them, and manage their lifecycle.

---

## 1. What Are Chart Dependencies?

Chart dependencies allow you to define other Helm charts that your chart relies on. These dependencies are managed in a centralized way, making it easier to reuse and compose charts.

### Common Use Cases:
- Including a database (e.g., PostgreSQL) as a dependency in your application chart.
- Reusing shared infrastructure components across multiple applications.

---

## 2. Defining Dependencies

Dependencies are specified in the `Chart.yaml` file of your chart.

### Syntax
```yaml
# Chart.yaml
apiVersion: v2
name: my-chart
version: 1.0.0
dependencies:
  - name: postgres
    version: "12.3.0"
    repository: "https://charts.bitnami.com/bitnami"
```

### Explanation
- **name**: Name of the dependent chart.
- **version**: Version of the dependency.
- **repository**: URL of the chart repository containing the dependency.

#### Installing Dependencies
Run the following command to download and install dependencies:
```bash
helm dependency update
```

This command creates a `charts/` directory with the specified dependencies.

---

## 3. Passing Values to Dependencies

You can pass custom values to dependencies through the `values.yaml` file or directly in the command line.

### Using `values.yaml`
```yaml
# values.yaml
postgres:
  auth:
    username: admin
    password: secret
```

### Using Command-Line Overrides
```bash
helm install my-release ./my-chart \
  --set postgres.auth.username=admin \
  --set postgres.auth.password=secret
```

---

## 4. Managing Dependency Values in Subcharts

To isolate values specific to a dependency, use namespaces in the `values.yaml` file.

### Example
```yaml
# values.yaml
postgres:
  fullnameOverride: my-custom-postgres
  persistence:
    size: 10Gi
```

These values are passed directly to the `postgres` chart.

---

## 5. Controlling Dependency Charts

Helm allows you to enable or disable dependencies dynamically using the `enabled` key in the `values.yaml` file.

### Example
```yaml
# values.yaml
postgres:
  enabled: true
```

#### Template Example
You can conditionally include a dependency in your templates:
```yaml
{{- if .Values.postgres.enabled }}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres-deployment
spec:
  replicas: 1
  ...
{{- end }}
```

---

## 6. Using Subchart Values

Subcharts can also have their own `values.yaml`. You can pass values to these subcharts using the parent chart's `values.yaml` file.

### Example
```yaml
# Parent chart's values.yaml
subchart1:
  subchart2:
    setting: value
```

---

## 7. Best Practices

1. **Pin Dependency Versions**: Always specify a version for dependencies to avoid unexpected updates.
2. **Isolate Dependency Configurations**: Use namespaces to organize values specific to dependencies.
3. **Update Dependencies Regularly**: Use `helm dependency update` to ensure you are working with the latest compatible versions.
4. **Document Your Dependencies**: Clearly document the purpose and configuration options for each dependency in your chart's `README.md`.

---

## 8. Full Example

### `Chart.yaml`
```yaml
apiVersion: v2
name: my-chart
version: 1.0.0
dependencies:
  - name: postgres
    version: "12.3.0"
    repository: "https://charts.bitnami.com/bitnami"
```

### `values.yaml`
```yaml
postgres:
  enabled: true
  auth:
    username: admin
    password: secret
  persistence:
    size: 20Gi
```

### Command to Install
```bash
helm dependency update
helm install my-release ./my-chart
```

This setup provisions a PostgreSQL instance as part of the application deployment and configures it with the specified settings.
