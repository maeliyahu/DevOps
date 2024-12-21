### Container Lifecycle Hooks

Container Lifecycle Hooks in Kubernetes are used to execute specific actions at certain points in a container's lifecycle. These hooks provide a mechanism to run custom code or scripts during critical stages such as before a container starts, after it has been initialized, or just before it shuts down. They help manage operational tasks like cleanup, logging, or other custom behaviors, ensuring that containers can be managed in a more automated and controlled manner.

There are three primary types of lifecycle hooks:

1. **Init Containers**: 
   Init containers are specialized containers that run before the main application container starts. They are useful for tasks that need to be completed before the main application can run, such as waiting for dependencies to be ready, setting up configurations, or preparing the environment. All init containers must complete successfully before the main container starts, and if any init container fails, the pod will not proceed to the main container. Init containers are executed sequentially, and each must finish before the next one starts.

   **Example**:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: init-container-example
   spec:
     initContainers:
     - name: init-myservice
       image: busybox
       command: ["sh", "-c", "echo 'Initializing service'"]
     containers:
     - name: my-container
       image: my-image
   ```
   In this example, the `initContainers` section runs a setup command before the main container starts, ensuring that initialization tasks are completed first.

2. **PostStart Hook**: 
   The `postStart` hook is executed immediately after a container starts. This hook is useful for running tasks that need to occur right after the container has been initialized but before the main application inside the container fully starts handling requests. You can use this hook to perform tasks like setting up configuration files, monitoring container readiness, or even initializing application services that need to run before the container begins its main operations.

3. **PreStop Hook**: 
   The `preStop` hook is triggered before a container is terminated, giving it a chance to gracefully shut down. This hook is helpful for tasks like cleaning up resources, draining connections, or notifying other services that the container is about to stop. A properly implemented `preStop` hook ensures that the container shuts down in a controlled manner, avoiding sudden disruptions to the system or service.

Both the `postStart` and `preStop` hooks can invoke a command or run an HTTP request to a service. Below is an example of how to define lifecycle hooks in a pod specification:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: lifecycle-hooks-example
spec:
  initContainers:
  - name: init-myservice
    image: busybox
    command: ["sh", "-c", "echo 'Initializing application setup'"]
  containers:
  - name: my-container
    image: my-image
    lifecycle:
      postStart:
        exec:
          command: ["/bin/sh", "-c", "echo 'Container started'"]
      preStop:
        exec:
          command: ["/bin/sh", "-c", "echo 'Container is stopping'"]
```

### Explanation:
- **Init Containers**: The `initContainers` section runs the command `echo 'Initializing application setup'` before the main container starts. This ensures that any initialization logic is handled before the container runs its primary workload.
- **PostStart Hook**: The `postStart` hook runs the command `echo 'Container started'` right after the container starts. This is useful for monitoring or performing configuration tasks once the container is up.
- **PreStop Hook**: The `preStop` hook runs the command `echo 'Container is stopping'` before the container stops, allowing for any cleanup or pre-shutdown tasks.

These hooks help automate container lifecycle management and ensure smoother operations within a Kubernetes environment. By leveraging lifecycle hooks, you can ensure that initialization, configuration, and shutdown tasks are handled seamlessly, providing better control and reliability for your applications running in containers.
