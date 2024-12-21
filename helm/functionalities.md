# Helm Template Common Functionalities

This guide provides detailed explanations and examples of common functionalities used in Helm templates. These features allow you to build dynamic, flexible, and reusable Helm charts for Kubernetes deployments.

---

## 1. Conditional Statements

### 1.1 `if` Statement
Used to include or exclude parts of a template based on a condition.

#### Syntax
```yaml
{{- if .Values.enabled }}
Feature is enabled
{{- end }}
```

#### Example
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: conditional-example
  namespace: default
data:
{{- if .Values.enabled }}
  configKey: "enabledValue"
{{- end }}
```

### 1.2 `if-else` Statement
Handles scenarios with two conditions.

#### Syntax
```yaml
{{- if .Values.enabled }}
Feature is enabled
{{- else }}
Feature is disabled
{{- end }}
```

#### Example
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: if-else-example
  namespace: default
data:
  configKey: |
{{- if .Values.enabled }}
    "enabledValue"
{{- else }}
    "disabledValue"
{{- end }}
```

---

## 2. `with` Statement
Used to simplify access to deeply nested values.

#### Syntax
```yaml
{{- with .Values.image }}
Image repository: {{ .repository }}
Image tag: {{ .tag }}
{{- end }}
```

#### Example
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: with-example
spec:
  template:
    spec:
      containers:
        - name: app
          image: {{- with .Values.image }}{{ .repository }}:{{ .tag }}{{- end }}
```

---

## 3. Loops

### 3.1 Looping Through a List
Used to iterate over a list of items.

#### Syntax
```yaml
{{- range .Values.items }}
- {{ . }}
{{- end }}
```

#### Example
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: list-loop-example
data:
  items: |
{{- range .Values.items }}
  - {{ . }}
{{- end }}
```

### 3.2 Looping Through a Map
Used to iterate over key-value pairs in a map.

#### Syntax
```yaml
{{- range $key, $value := .Values.configs }}
{{ $key }}: {{ $value }}
{{- end }}
```

#### Example
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: map-loop-example
data:
{{- range $key, $value := .Values.configs }}
  {{ $key }}: "{{ $value }}"
{{- end }}
```

---

## 4. Variables

### Defining Variables
Variables store values for reuse in templates.

#### Syntax
```yaml
{{- $variableName := value }}
```

#### Example
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: variable-example
data:
  replicas: |
    {{- $replicas := .Values.replicaCount }}
    The number of replicas is {{ $replicas }}.
```

---

## 5. Default Values

The `default` function provides fallback values when a variable or key is not set.

#### Syntax
```yaml
{{ default "defaultValue" .Values.optionalKey }}
```

#### Example
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: default-example
data:
  replicas: |
    {{ default 3 .Values.replicaCount }}
```

---

## 6. Combining Functionalities

### Example Combining Loops, Conditions, and Variables
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: combined-example
data:
  configs: |
    {{- range $key, $value := .Values.configurations }}
    {{- if $value.enabled }}
    {{ $key }}: {{ $value.setting }}
    {{- end }}
    {{- end }}
```

---

Helm templates offer robust features for dynamic chart generation. Mastering these common functionalities will allow you to create efficient and reusable charts tailored to complex Kubernetes deployments.
