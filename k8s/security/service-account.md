### Kubernetes Service Accounts

**Service Accounts** in Kubernetes are a type of account intended for use by applications running in Pods. They provide an identity for processes in a Pod to interact with the Kubernetes API and other resources securely.

---

#### 1. **Key Concepts of Service Accounts**

- **Default Service Account**: Every namespace has a default service account automatically assigned to Pods if no other service account is specified.
- **Custom Service Accounts**: Users can create and assign custom service accounts to Pods for specific permissions.
- **Tokens**: Service accounts are associated with secrets containing tokens, which are used for authentication.

---

#### 2. **Service Account Example**

##### Creating a Service Account
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default
```

##### Using a Service Account in a Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service-account
  containers:
    - name: my-container
      image: nginx
```

In this example:
- The `my-pod` Pod uses the `my-service-account` service account to authenticate with the Kubernetes API.

---

#### 3. **Default Behavior**

- Kubernetes automatically assigns the **default service account** to Pods if no custom service account is specified.
- The default service account may have limited permissions. It's recommended to create custom service accounts for applications requiring specific access.

---

#### 4. **Best Practices**

- **Assign Specific Service Accounts**: Avoid using the default service account for production workloads.
- **Use RBAC**: Pair service accounts with RBAC roles to grant precise permissions.
- **Restrict Access**: Only grant the minimum permissions required for a service account.
- **Automount Control**: Disable automatic token mounting if the Pod does not need API access:
  ```yaml
  spec:
    automountServiceAccountToken: false
  ```

---

#### 5. **Managing Service Accounts**

- **Create a Service Account**:
  ```bash
  kubectl create serviceaccount <service-account-name> -n <namespace>
  ```

- **View Service Accounts**:
  ```bash
  kubectl get serviceaccounts -n <namespace>
  ```

- **Describe a Service Account**:
  ```bash
  kubectl describe serviceaccount <service-account-name> -n <namespace>
  ```

- **Delete a Service Account**:
  ```bash
  kubectl delete serviceaccount <service-account-name> -n <namespace>
  ```

---

#### 6. **Service Account Tokens**

Service accounts are associated with **Secrets** that store the authentication token for the account. These tokens are used by Pods to access the Kubernetes API.

##### Viewing the Token Secret
```bash
kubectl get secret -n <namespace>
```

##### Decoding the Token
```bash
kubectl get secret <secret-name> -n <namespace> -o jsonpath='{.data.token}' | base64 --decode
```

---

#### 7. **Use Cases**

- **Application Authentication**: Use service accounts to authenticate applications running in Pods with the Kubernetes API.
- **Granular Permissions**: Combine service accounts with RBAC to restrict or grant access to cluster resources.
- **Multi-Tenancy**: Assign different service accounts to applications based on their access requirements.

---

#### 8. **Conclusion**

Service accounts are essential for securing and managing application access to the Kubernetes API. By creating and assigning custom service accounts and pairing them with RBAC, administrators can ensure that workloads have only the access they require, reducing security risks and improving cluster management.

