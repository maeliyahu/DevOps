# Helm Hooks Guide

Helm hooks provide a way to attach custom actions to the lifecycle of a Helm release. These actions are executed at specific points during the release process, enabling advanced workflows such as pre-installation configuration or post-deployment cleanup.

---

## 1. What Are Helm Hooks?

Hooks are Kubernetes manifests annotated with specific Helm lifecycle events. Helm triggers these hooks at predefined stages of the release lifecycle.

### Common Use Cases:
- Creating resources required only during installation or upgrade (e.g., initialization jobs).
- Running cleanup tasks after resource deletion.
- Executing migrations before deploying an updated release.

---

## 2. Hook Annotations

To define a hook, annotate a Kubernetes manifest with `helm.sh/hook`. Multiple hooks can be applied to the same resource.

### Example
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pre-install-job
  annotations:
    "helm.sh/hook": pre-install
```

---

## 3. Hook Lifecycle Events

Helm supports the following hooks:

| Hook                | Description                                          |
|---------------------|------------------------------------------------------|
| `pre-install`       | Runs before any resources are installed.            |
| `post-install`      | Runs after all resources are installed.             |
| `pre-delete`        | Runs before any resources are deleted.              |
| `post-delete`       | Runs after all resources are deleted.               |
| `pre-upgrade`       | Runs before any resources are upgraded.             |
| `post-upgrade`      | Runs after all resources are upgraded.              |
| `pre-rollback`      | Runs before any resources are rolled back.          |
| `post-rollback`     | Runs after all resources are rolled back.           |
| `test`              | Used for Helm test tasks.                           |

---

## 4. Hook Weight

You can control the execution order of hooks using the `helm.sh/hook-weight` annotation. Hooks with lower weights run first.

### Example
```yaml
metadata:
  annotations:
    "helm.sh/hook": pre-install
    "helm.sh/hook-weight": "-10"
```

---

## 5. Deleting Hook Resources

Hook resources are not automatically deleted by default. You can control this behavior using the `helm.sh/hook-delete-policy` annotation.

### Available Policies:
- `hook-succeeded`: Delete the resource if the hook succeeds.
- `hook-failed`: Delete the resource if the hook fails.
- `before-hook-creation`: Delete the resource before a new hook is created.

### Example
```yaml
metadata:
  annotations:
    "helm.sh/hook": post-install
    "helm.sh/hook-delete-policy": hook-succeeded
```

---

## 6. Where to Define Hook Files in a Helm Project

Hook resources are defined in the `templates/` directory of your Helm chart, alongside other Kubernetes manifests. 

### Recommended File Organization
While there is no specific directory for hooks, it's good practice to organize hook files with descriptive names to make them easier to manage, such as:

```
my-helm-chart/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── _helpers.tpl
│   ├── pre-install-hooks.yaml  # Hooks for pre-install event
│   ├── post-upgrade-hooks.yaml # Hooks for post-upgrade event
```

### Example File: `templates/pre-install-hooks.yaml`
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pre-install-job
  annotations:
    "helm.sh/hook": pre-install
    "helm.sh/hook-weight": "0"
    "helm.sh/hook-delete-policy": hook-succeeded
spec:
  template:
    spec:
      containers:
      - name: init-container
        image: busybox
        command: ["/bin/sh", "-c", "echo Pre-install task completed"]
      restartPolicy: OnFailure
```

---

## 7. Full Example

### `templates/hooks.yaml`
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: migrate-db
  annotations:
    "helm.sh/hook": pre-upgrade
    "helm.sh/hook-weight": "0"
    "helm.sh/hook-delete-policy": hook-succeeded
spec:
  template:
    spec:
      containers:
      - name: db-migrator
        image: my-db-migrator:1.0
        command: ["/bin/bash", "-c", "run migrations"]
      restartPolicy: OnFailure
```

---

## 8. Debugging Hooks

To debug hooks, you can use the `helm install` or `helm upgrade` commands with the `--debug` flag.

```bash
helm install my-release ./my-chart --debug
```

This will display detailed information about the hooks being executed.

---

## 9. Best Practices

1. **Avoid Long-Running Hooks**: Hooks should not block the release process for extended periods. Use asynchronous jobs if needed.
2. **Use Weights for Complex Workflows**: Use `hook-weight` to manage the execution order when multiple hooks are involved.
3. **Clean Up Resources**: Always use `helm.sh/hook-delete-policy` to avoid orphaned resources.
4. **Test Hooks**: Ensure your hooks are thoroughly tested in various scenarios to avoid unexpected failures.

---

Helm hooks are a powerful feature that extends the capabilities of your charts, enabling advanced deployment workflows. Use them wisely to enhance the flexibility and reliability of your Helm releases!
