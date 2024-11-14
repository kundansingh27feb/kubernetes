# Kubernetes Service

In Kubernetes, a Service is an abstract way to expose an application running on a set of Pods. Services are used to allow different parts of your application to communicate, manage load balancing, and expose your applications to external or internal users. In Kubernetes, Services provide stable IP addresses and DNS names, which allow reliable access to the pods even if they restart or scale.

## 1. ClusterIP Service

**Description:** The default type of service. It makes the service accessible only within the AKS cluster using an internal IP address. This type is ideal for communication between internal applications that do not need to be accessible from outside the cluster.

**Use Case:** You might use a ClusterIP service to connect microservices within the AKS cluster. For instance, a frontend application could use a ClusterIP to communicate with a backend API service.

```
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  type: ClusterIP
  selector:
    app: backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

**How it works:** This service routes requests made within the cluster to the backend pods that match the app: backend label selector. It is not accessible from outside the AKS cluster.

## 2. NodePort Service

**Description:** Exposes the service on a static port on each AKS node’s IP address. This allows access to the service from outside the cluster by reaching any node on the specified port. However, this approach isn’t highly scalable or secure for production use.

**Use Case:** You could use NodePort for testing purposes or internal applications that need limited external access without a load balancer.

```
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  type: NodePort
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30001
```

**How it works:** The service is exposed on port 30001 on each node's IP address. Accessing http://<node-ip>:30001 will route traffic to the frontend application pods within the cluster.

## 3. LoadBalancer Service

**Description:** A LoadBalancer service type automatically provisions an Azure Load Balancer to route traffic from the internet to the AKS cluster. This service type provides an external IP address, enabling direct access to applications from outside the AKS cluster.

**Use Case:** This type is commonly used to expose web applications or APIs to the internet, making it ideal for production applications that require external access.

```
apiVersion: v1
kind: Service
metadata:
  name: public-service
spec:
  type: LoadBalancer
  selector:
    app: public-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

**How it works:** This configuration provisions an external load balancer in Azure, which receives requests on port 80 and forwards them to port 8080 on the public-app pods. The load balancer is assigned a public IP, making it accessible from outside the cluster at http://<external-ip>:80.

## 4. ExternalName Service

**Description:** An ExternalName service maps a Kubernetes service name to an external DNS name. It doesn’t create a proxy or expose any pods directly; instead, it allows applications inside the AKS cluster to access an external service using a DNS name.

**Use Case:** ExternalName is helpful for accessing external resources, such as a managed database service (e.g., an Azure SQL Database) or a third-party API, from within the AKS cluster without needing direct IP management.

```
apiVersion: v1
kind: Service
metadata:
  name: external-service
spec:
  type: ExternalName
  externalName: api.example.com
```

**How it works:** This service provides access to api.example.com from within the AKS cluster by making it accessible under the external-service name. Applications in the cluster can simply use the service name external-service to reach the external API.
