### Kubernetes Ingress

An **Ingress** in Kubernetes is a collection of rules that allow inbound connections to reach the cluster services. It provides HTTP and HTTPS routing to services based on the request URL, host, and other parameters, such as paths. An Ingress is a more advanced and flexible way of exposing services compared to traditional Service types like `ClusterIP`, `NodePort`, and `LoadBalancer`.

Ingress enables the use of host-based and path-based routing, SSL/TLS termination, and virtual hosts, making it a powerful tool for managing external access to services in a Kubernetes cluster.

#### 1. **Ingress Controller**
An **Ingress Controller** is the component that listens for Ingress resource definitions and implements the rules defined in the Ingress. The Ingress resource itself does not provide the functionality for routing; instead, it relies on an Ingress Controller (like Nginx, Traefik, HAProxy, or others) to perform the actual routing of traffic to the backend services.

**Example: Nginx Ingress Controller Deployment**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ingress-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-ingress
  template:
    metadata:
      labels:
        app: nginx-ingress
    spec:
      containers:
        - name: nginx-ingress-controller
          image: nginx-ingress-controller:latest
          ports:
            - name: http
              containerPort: 80
            - name: https
              containerPort: 443
```

In this example:
- The **Nginx Ingress Controller** is deployed to handle HTTP(S) traffic for all Ingress rules in the cluster.

#### 2. **Ingress Resource**
The **Ingress resource** defines the rules for routing HTTP(S) traffic to the appropriate services in the cluster. It specifies the URL paths, hostnames, and the services that should handle the requests.

**Example: Ingress Resource**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 8080
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
  tls:
  - hosts:
    - myapp.example.com
    secretName: myapp-tls
```

In this example:
- The **Ingress resource** defines two routes for `myapp.example.com`: `/api` and `/web`.
  - Traffic to `/api` is routed to the `api-service` on port 8080.
  - Traffic to `/web` is routed to the `web-service` on port 80.
- **TLS termination** is configured with the `myapp-tls` secret, which contains the SSL certificate for securing the traffic.

#### 3. **TLS/SSL Termination**
Ingress can handle TLS/SSL termination, meaning it can manage secure HTTPS traffic by decrypting the traffic before forwarding it to the backend services. TLS termination is typically configured by specifying a TLS section in the Ingress resource and associating a secret containing the SSL certificate and key.

**Example: TLS Termination in Ingress**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
  tls:
  - hosts:
    - myapp.example.com
    secretName: myapp-tls
```

In this example:
- The **Ingress** resource enables TLS termination using the `myapp-tls` secret for `myapp.example.com`.
- Incoming HTTPS requests are decrypted by the Ingress Controller, and the traffic is forwarded as HTTP to the `my-service` service.

#### 4. **Path and Host-based Routing**
Ingress allows you to define rules based on the **host** and **path**. This enables routing of different types of traffic to different services based on the URL or hostname.

**Example: Path-based Routing**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-path-based-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 8080
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

In this example:
- Requests to `myapp.example.com/api` are routed to the `api-service` on port 8080.
- Requests to `myapp.example.com/web` are routed to the `web-service` on port 80.

**Example: Host-based Routing**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-host-based-ingress
spec:
  rules:
  - host: api.myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 8080
  - host: web.myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

In this example:
- Requests to `api.myapp.example.com` are routed to the `api-service` on port 8080.
- Requests to `web.myapp.example.com` are routed to the `web-service` on port 80.

#### 5. **Ingress Annotations**
Ingress annotations are used to customize the behavior of the Ingress Controller. These annotations vary depending on the Ingress Controller used, such as Nginx, Traefik, or HAProxy.

**Example: Using Annotations with Nginx Ingress Controller**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
  tls:
  - hosts:
    - myapp.example.com
    secretName: myapp-tls
```

In this example:
- The **nginx.ingress.kubernetes.io/rewrite-target** annotation rewrites the URL path before forwarding the request to the backend service.
- The **nginx.ingress.kubernetes.io/ssl-redirect** annotation forces HTTP requests to be redirected to HTTPS.

#### Conclusion

The **Ingress** resource in Kubernetes provides a powerful way to manage external access to services by defining rules for routing HTTP(S) traffic. With support for host-based and path-based routing, SSL/TLS termination, and a variety of annotations, Ingress allows you to build flexible and scalable ingress strategies for your applications.

- **Ingress Controller** implements the routing rules defined in the Ingress resource.
- **Ingress Resource** defines how external traffic is routed to services based on hosts and paths.
- **TLS termination** allows for secure HTTPS traffic management.
- **Annotations** provide additional customizability for the Ingress Controller.

By using Ingress, you can efficiently manage and route traffic into your Kubernetes cluster while ensuring that your applications are accessible and secure.
