# Kubernetes Secrets Documentation

## Overview
Kubernetes Secrets are used to store and manage sensitive information such as passwords, OAuth tokens, and SSH keys. Secrets help keep sensitive data secure by limiting its exposure within the cluster.

### Types of Secrets
1. **Opaque**: Generic key-value pairs.
2. **Docker Config**: For storing Docker credentials.
3. **TLS**: To store a TLS certificate and key.

---

## Creating a Secret

### Using `kubectl` Command

#### Opaque Secret Example:
```bash
kubectl create secret generic my-secret \
  --from-literal=username=admin \
  --from-literal=password=password123
```

#### Secret from File Example:
```bash
kubectl create secret generic my-secret \
  --from-file=key1=path/to/file1 \
  --from-file=key2=path/to/file2
```

#### TLS Secret Example:
```bash
kubectl create secret tls tls-secret \
  --cert=path/to/tls.cert \
  --key=path/to/tls.key
```

### Using a YAML Manifest
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
  namespace: default
stringData:
  username: admin
  password: password123
```
Apply the manifest:
```bash
kubectl apply -f secret.yaml
```

---

## Using Secrets in Pods

### Projecting Secrets as Environment Variables

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-example
spec:
  containers:
    - name: app-container
      image: busybox
      command: ["sh", "-c", "echo $SECRET_USERNAME && echo $SECRET_PASSWORD && sleep 3600"]
      env:
        - name: SECRET_USERNAME
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: username
        - name: SECRET_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: password
```

### Projecting Secrets as Volumes

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-volume-example
spec:
  containers:
    - name: app-container
      image: busybox
      command: ["sh", "-c", "cat /etc/secret-data/username && cat /etc/secret-data/password && sleep 3600"]
      volumeMounts:
        - name: secret-volume
          mountPath: /etc/secret-data
          readOnly: true
  volumes:
    - name: secret-volume
      secret:
        secretName: my-secret
        items:
          - key: username
            path: username
          - key: password
            path: password
```

---

## Best Practices
- **Use RBAC**: Ensure that only authorized pods and users have access to secrets.
- **Encrypt Secrets at Rest**: Enable secret encryption in your Kubernetes cluster.
- **Rotate Secrets Regularly**: Regularly update secrets to minimize the impact of a potential breach.
- **Avoid Hardcoding**: Never hardcode secrets in your application code.

---

## Viewing Secrets
Secrets are base64 encoded. To view a secret:
```bash
kubectl get secret my-secret -o yaml
```

Decode the value:
```bash
echo "encoded-value" | base64 --decode
```

---

This documentation provides an overview of how to create, use, and manage Kubernetes secrets. For more details, refer to the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/secret/).
