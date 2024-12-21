# Kubernetes Pod Configuration Options

Kubernetes (k8s) pod configuration options are primarily defined in the pod's manifest file, typically written in YAML. Below is a structured summary of key configuration sections:

---

## 1. Metadata
Metadata contains information about the pod and is used for identification and categorization.

- **name**: Unique name for the pod.
- **namespace**: Namespace in which the pod is created.
- **labels**: Key-value pairs for categorizing the pod.
- **annotations**: Key-value pairs for additional metadata (non-selective).

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: metadata-example-pod
  namespace: default
  labels:
    app: my-app
    environment: production
  annotations:
    description: "This is a pod for demonstrating metadata configuration."
```

---

## 2. Spec
The `spec` section defines the behavior of the pod and its containers.

### **Containers**
Each container has the following configurations:
- **name**: Name of the container.
- **image**: Container image to run.
- **ports**: Ports exposed by the container.
- **command**: Command to execute in the container (overrides image's default).
- **args**: Arguments to the command.
- **env**: Environment variables.
- **envFrom**: Bulk import of environment variables from ConfigMaps or Secrets.
- **resources**: Resource requests and limits (CPU, memory).
- **volumeMounts**: Mount points for volumes within the container.
- **livenessProbe**: Defines how to check if the container is alive.
- **readinessProbe**: Defines how to check if the container is ready to serve requests.
- **startupProbe**: Defines how to check if the container has started properly.
- **lifecycle**: Defines actions to take at container lifecycle events (e.g., `postStart`, `preStop`).
- **securityContext**: Security settings, such as user ID, group ID, capabilities, and privileges.
- **stdin**, **tty**: Enable interactive shell features.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: containers-example-pod
spec:
  containers:
    - name: app-container
      image: nginx:latest
      ports:
        - containerPort: 80
      command: ["nginx"]
      args: ["-g", "daemon off;"]
      env:
        - name: ENVIRONMENT
          value: "production"
      resources:
        requests:
          memory: "128Mi"
          cpu: "500m"
        limits:
          memory: "256Mi"
          cpu: "1"
      livenessProbe:
        httpGet:
          path: /healthz
          port: 80
        initialDelaySeconds: 5
        periodSeconds: 10
      readinessProbe:
        httpGet:
          path: /readiness
          port: 80
        initialDelaySeconds: 5
        periodSeconds: 10
      startupProbe:
        httpGet:
          path: /healthz
          port: liveness-port
        failureThreshold: 30
        periodSeconds: 10
      securityContext:
        runAsUser: 1000
        runAsGroup: 3000
        fsGroup: 2000
```

---

### **Volumes**
Defines storage for the pod.
- **Types**: ConfigMap, Secret, EmptyDir, HostPath, PersistentVolumeClaim, etc.
- **name**: Unique volume name.
- **volumeSource**: Configuration for the volume type.

---

### **Node Affinity and Scheduling**
- **nodeSelector**: Specifies node selection based on labels.
- **affinity**: Advanced scheduling preferences (node and pod affinity/anti-affinity).
- **tolerations**: Defines tolerations for taints on nodes.

---

### **Networking**
- **hostname**: Hostname of the pod.
- **subdomain**: Subdomain used for DNS resolution.
- **dnsPolicy**: DNS resolution policy (e.g., ClusterFirst, Default).
- **hostNetwork**: Whether the pod uses the node’s network namespace.
- **hostAliases**: Custom `/etc/hosts` entries.
- **ports**: Network ports exposed by the pod's containers.

---

### **Security**
- **securityContext**: Pod-level security settings (e.g., FSGroup, SELinux, runAsUser).
- **serviceAccountName**: Specifies the ServiceAccount used by the pod.
- **automountServiceAccountToken**: Whether to automatically mount a service account token.

---

### **Runtime Settings**
- **restartPolicy**: Restart policy (`Always`, `OnFailure`, `Never`).
- **priorityClassName**: Priority class for the pod.
- **terminationGracePeriodSeconds**: Time to wait before forcibly terminating the pod.
- **dnsConfig**: Custom DNS configuration.

---

### **Other Configurations**
- **imagePullSecrets**: Secrets for accessing private container registries.
- **hostAliases**: Custom host file entries.
- **shareProcessNamespace**: Enables process namespace sharing between containers.

---

## 3. Status
The `status` section is read-only and updated by Kubernetes to report the current state of the pod:
- **phase**: Current phase (`Pending`, `Running`, `Succeeded`, `Failed`, `Unknown`).
- **conditions**: Detailed status conditions (`Ready`, `Initialized`, `PodScheduled`).
- **hostIP**, **podIP**: IP addresses.
- **startTime**: Pod's start time.

---

This summary outlines the major configurations available for a Kubernetes pod. Each section allows for detailed customization to fit your application's needs. Let me know if you’d like examples for specific sections!
