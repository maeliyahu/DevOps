# Helm Guide

This guide provides a comprehensive overview of Helm, the package manager for Kubernetes. It includes key concepts, installation steps, commands, and examples for using Helm effectively.

---

## 1. Overview of Helm
Helm is a tool that helps you manage Kubernetes applications using charts, which are pre-configured Kubernetes resources.

### Key Concepts
- **Charts**: Packages of pre-configured Kubernetes resources.
- **Releases**: Instances of a chart running in a Kubernetes cluster.
- **Repositories**: Collections of charts.

### Benefits of Helm
- Simplifies application deployments.
- Facilitates versioning and rollbacks.
- Supports parameterization and templating.

---

## 2. Installing Helm

### Using Script
```bash
curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

### Using Package Manager
#### macOS:
```bash
brew install helm
```
#### Linux (APT):
```bash
sudo apt-get install -y apt-transport-https --no-install-recommends \
    && curl -fsSL https://baltocdn.com/helm/signing.asc | sudo apt-key add - \
    && echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list \
    && sudo apt-get update && sudo apt-get install -y helm
```

### Verifying Installation
```bash
helm version
```

---

## 3. Basic Helm Commands

### Adding a Repository
```bash
helm repo add stable https://charts.helm.sh/stable
```

### Listing Repositories
```bash
helm repo list
```

### Searching for Charts
```bash
helm search repo nginx
```

### Installing a Chart
```bash
helm install my-release stable/nginx
```

### Upgrading a Release
```bash
helm upgrade my-release stable/nginx
```

### Uninstalling a Release
```bash
helm uninstall my-release
```

### Listing Releases
```bash
helm list
```

---

## 4. Helm Chart Structure

### Directory Layout
```plaintext
my-chart/
  Chart.yaml        # Chart metadata
  values.yaml       # Default configuration values
  templates/        # Kubernetes resource templates
  charts/           # Dependency charts
  README.md         # Documentation for the chart
```

### Example `Chart.yaml`
```yaml
apiVersion: v2
name: my-chart
description: A sample Helm chart
version: 0.1.0
authors:
  - name: Example Author
    email: example@example.com
```

---

## 5. Using Values

### Setting Values Inline
```bash
helm install my-release stable/nginx --set replicaCount=3
```

### Using a Values File
```bash
helm install my-release stable/nginx -f custom-values.yaml
```

### Example `values.yaml`
```yaml
replicaCount: 2
image:
  repository: nginx
  tag: latest
resources:
  requests:
    memory: "128Mi"
    cpu: "500m"
```

---

## 6. Helm Templates

### Templating Example
```yaml
# templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-deployment
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}
    spec:
      containers:
        - name: nginx
          image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
```

### Rendering Templates
```bash
helm template my-release ./my-chart
```

---

## 7. Helm Release Management

### Viewing Release Details
```bash
helm status my-release
```

### Rolling Back a Release
```bash
helm rollback my-release 1
```

### Deleting Release History
```bash
helm uninstall my-release --keep-history
```

---

## 8. Advanced Usage

### Using Dependencies
Add dependencies to `Chart.yaml`:
```yaml
dependencies:
  - name: redis
    version: "6.0.0"
    repository: https://charts.bitnami.com/bitnami
```

### Managing Dependencies
```bash
helm dependency update
```

### Example with Subchart
```bash
helm install my-release ./my-chart --set redis.enabled=true
```

---

This guide serves as a reference for using Helm with Kubernetes. For more details, refer to the [official Helm documentation](https://helm.sh/docs/).
