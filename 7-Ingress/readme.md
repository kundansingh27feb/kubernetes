# Ingress

Ingress is an API object in Kubernetes that provides HTTP and HTTPS routing to services within a cluster. It acts as a smart entry point that defines how external traffic is directed to the services running inside your Kubernetes cluster. Ingress eliminates the need for creating individual load balancers or exposing services individually using NodePort or LoadBalancer types.

## How Ingress Works

### Ingress Controller:

An Ingress resource works in conjunction with an Ingress Controller, which is a Kubernetes application responsible for interpreting and fulfilling Ingress objects.

1. **Commonly used Ingress Controllers include:**

- NGINX Ingress Controller
- Traefik
- AWS ALB Ingress Controller
- GCP Ingress Controller

2. **Components of Ingress:**

- **Host-based Routing:** Directs traffic based on the requested hostname (e.g., example.com).
- **Path-based Routing:** Routes traffic to backend services based on the URL path (e.g., /app or /api).
- **TLS:** Supports SSL termination for secure communication.

3. **How It Works:**

- When a request hits the Ingress Controller (e.g., NGINX), it matches the host and path rules defined in the Ingress resource.
- The controller forwards the request to the appropriate backend service and ensures a smooth flow of traffic.

## Configuring Ingress with NGINX In Kubernetes

### Step 1: Install NGINX Ingress Controller Using yaml

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

### Step 2: Create a Deployment for Your Application

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-angular
  labels:
    app: webapp
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp-angular
        image: kundansingh27feb/webapp-angular:v2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "0.5m"
            memory: "512Mi"
          limits:
            cpu: "0.6m"
            memory: "600Mi"
```

### Step 3: Expose the Application as a Service

```
apiVersion: v1
kind: Service 
metadata:
  name: webapp-angular-service
  labels:
    app: webapp-service
spec: 
  selector:
    app: webapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
```

### Step 4: Define an Ingress Resource

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-angular-ingress
spec:
  rules:
  - host: angular.gurukulsiksha.com
    http:
    - paths:
      path: /
      pathType: Prefix
      backend:
        service:
          name: webapp-angular-service
          port:
            number: 80
```

### Step 5: Test the Ingress

- Add a DNS entry for example.com pointing to your Ingress Controller’s public IP or add it to your local /etc/hosts file.
- Use a browser or curl to access the application:

curl http://angular.gurukulsiksha.com

## Key Points:

- **Ingress Controllers are essential:** Without one, the Ingress resource won’t work.
- **Annotations control behavior:** NGINX-specific annotations (like rewrite-target) add flexibility to the configuration.
- **TLS Support:** Enable secure communication by configuring tls in the Ingress resource.
- This setup allows you to route traffic efficiently to multiple services in your Kubernetes cluster.

# How To TLS Cert With INgress

To use TLS cert with ingress here we will use self signed certificate.

### Step 1: Obtain a TLS Certificate

You can use:

- Let's Encrypt: A free, automated, and open certificate authority.
- Self-Signed Certificate: For testing purposes.
- Purchased Certificate: From a trusted Certificate Authority (CA).

**Create a Self-Signed Certificate (for Testing):**

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=gurukulsiksha.com/O=angular"
```

**This creates**

- tls.crt (Certificate)
- tls.key (Private Key)

### Step 2: Create a Kubernetes Secret

**Store the certificate and key in a Kubernetes secret:**

```
kubectl create secret tls nginx-tls-secret --cert=tls.crt --key=tls.key
```

### Step 3: Update the Ingress Resource

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-angular-ingress
spec:
  tls:
  - hosts:
    - angular.gurukulsiksha.com
    secretName: nginx-tls-secret
  rules:
  - host: angular.gurukulsiksha.com
    http:
    - paths:
      path: /
      pathType: Prefix
      backend:
        service:
          name: webapp-angular-service
          port:
            number: 80
```

### Key points:

- tls specifies the host and the secretName that contains the certificate.
- hosts must match the domain the certificate was issued for (e.g., angular.gurukulsiksha.com).


### Step 4: Test HTTPS Access

- Update your /etc/hosts file or DNS to resolve example.com to your Ingress Controller’s IP.
- Access the application via HTTPS:

```
curl -k https://angular.gurukulsiksha.com
```

The -k flag is used for self-signed certificates. For production, it should work without this flag.

<hr>