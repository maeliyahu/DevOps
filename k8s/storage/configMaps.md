### Kubernetes ConfigMaps

**ConfigMaps** in Kubernetes are used to store non-confidential configuration data in key-value pairs. They allow you to decouple configuration artifacts from application code, making applications more portable and easier to manage.

---

#### 1. **Key Concepts of ConfigMaps**

- **Non-Confidential Data**: Used for storing non-sensitive information such as configuration files, environment variables, or command-line arguments.
- **Decoupling Configuration**: Enables separation of configuration from the application image.
- **Multiple Use Cases**: Can be consumed by Pods as environment variables, command-line arguments, or mounted as files.

---

#### 2. **Creating a ConfigMap**

##### Example 1: Directly from a YAML File
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
  namespace: default
data:
  database_url: "mysql://db.example.com:3306"
  log_level: "debug"
```

##### Example 2: From the Command Line
```bash
kubectl create configmap my-config --from-literal=database_url="mysql://db.example.com:3306" --from-literal=log_level="debug"
```

---

#### 3. **Consuming a ConfigMap in a Pod**

##### As Environment Variables
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
      env:
        - name: DATABASE_URL
          valueFrom:
            configMapKeyRef:
              name: my-config
              key: database_url
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: my-config
              key: log_level
```

##### Mounted as Files
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: my-config
```

In this example:
- The keys from the ConfigMap become files under `/etc/config`, and their values are written into those files.

---

#### 4. **Managing ConfigMaps**

- **Create a ConfigMap**:
  ```bash
  kubectl create configmap <configmap-name> --from-literal=<key>=<value>
  ```

- **View ConfigMaps**:
  ```bash
  kubectl get configmaps -n <namespace>
  ```

- **Describe a ConfigMap**:
  ```bash
  kubectl describe configmap <configmap-name> -n <namespace>
  ```

- **Delete a ConfigMap**:
  ```bash
  kubectl delete configmap <configmap-name> -n <namespace>
  ```

---

#### 5. **Best Practices**

- **Separate Sensitive Data**: Use **Secrets** for sensitive information instead of ConfigMaps.
- **Use Immutable ConfigMaps**: To prevent accidental updates, create immutable ConfigMaps:
  ```bash
  kubectl create configmap <name> --from-literal=<key>=<value> --immutable
  ```
- **Versioning**: Use naming conventions or annotations to track versions of ConfigMaps for rollback purposes.
- **Organize Configurations**: Group related keys in the same ConfigMap for simplicity.

---

#### 6. **Use Cases**

- **Application Configuration**: Store and pass environment-specific variables such as database URLs or API endpoints.
- **Configuration Files**: Mount configuration files (e.g., `nginx.conf`) from a ConfigMap to a container.
- **Command-Line Arguments**: Pass ConfigMap values as arguments to container commands.

---

#### 7. **Conclusion**

ConfigMaps in Kubernetes provide a flexible and powerful way to manage application configuration without embedding it in application images. By leveraging ConfigMaps effectively, you can simplify application deployment, improve portability, and maintain clean separation of configuration and code.

