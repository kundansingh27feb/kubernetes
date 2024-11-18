# Pod

A Pod in Kubernetes is the smallest and simplest unit in the Kubernetes object model. A pod represents one or more tightly coupled containers that share the same network namespace, storage, and configuration. It serves as a wrapper for a single or multiple containers, often encapsulating an application or part of an application. Pods ensure that containers within them can communicate with each other efficiently and share resources like storage volumes.

## Key Features of a Pod:


**Shared Network:** All containers in a pod share the same network namespace, IP address, and ports.


**Shared Storage:** Pods can mount shared storage volumes that are accessible by all containers within the pod.


**Single or Multiple Containers:**

- Single-container pods are the most common.
- Multi-container pods are used when containers need to work together closely, such as sidecar patterns (e.g., a logging or monitoring container).

## How to Create a Pod Using YAML

### Example 1: Single-container Pod:

```
apiVersion: v1
kind: Pod
metadata:
  name: single-container-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80
```

**Description:**


- Creates a pod named single-container-pod running an nginx container.
- The container listens on port 80.

### Example 2: Multi-container Pod

```
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: app-container
    image: my-app:latest
    ports:
    - containerPort: 8080
  - name: sidecar-container
    image: log-collector:latest
    args: ["--log-path=/var/log/app"]
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/app
  volumes:
  - name: shared-logs
    emptyDir: {}
```

**Description:**

- Creates a pod named multi-container-pod with two containers:
- app-container runs the main application.
- sidecar-container collects logs from the application using a shared volume shared-logs.

### Example 3: Pod with Environment Variables:

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-env
spec:
  containers:
  - name: app-container
    image: my-app:latest
    env:
    - name: ENVIRONMENT
      value: production
    - name: DEBUG
      value: "false"
```

**Description:**

- Sets environment variables (ENVIRONMENT and DEBUG) for the container within the pod.

### Example 4: Pod with Resource Requests and Limits

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-resources
spec:
  containers:
  - name: app-container
    image: my-app:latest
    resources:
      requests:
        memory: "512Mi"
        cpu: "500m"
      limits:
        memory: "1Gi"
        cpu: "1"
```

**Description:**

- Configures the container to request 512Mi of memory and 500m (0.5) CPU units.
- Sets a limit of 1Gi memory and 1 CPU.

## How to Apply a YAML Definition

- Save the YAML content to a file, e.g., pod-definition.yaml.

- Apply the YAML using kubectl:

```
kubectl apply -f pod-definition.yaml
```

- Verify the Pod's status:

```
kubectl get pods
```

## Real-world Use Cases

- **Web Server Pod:** A single-container pod running a web application, such as nginx, to serve content.
- **Sidecar Logging:** A multi-container pod with a logging sidecar to aggregate and forward logs.
- **Data Processing:** A pod with persistent storage to process data stored on an external volume.
- **CI/CD Tools:** Pods running CI/CD tools (e.g., Jenkins agents) that require environment variables and resource constraints.