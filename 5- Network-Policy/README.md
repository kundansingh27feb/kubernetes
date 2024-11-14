# Network Policies

A Network Policy in Azure Kubernetes Service (AKS) is used to control network traffic between pods. Network policies help secure communication by specifying which pods can communicate with each other and which cannot, enhancing security in your Kubernetes environment. You can configure network policies using the Kubernetes NetworkPolicy API, which allows for traffic filtering rules based on factors like pod labels, namespaces, and traffic types (ingress and egress). 

## How to Use Network Policy:

**Enable Network Policy:** When creating the K8S cluster, specify a network policy provider like Calico Network Policy.

**Define Network Policies:** Network policies are defined in YAML files that specify how traffic is allowed or denied between different pods and/or namespaces. 

## Network Policy YAML Configuration:

The basic structure of a NetworkPolicy YAML file includes:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**apiVersion:** The API version for network policies (networking.k8s.io/v1).

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**kind:** Specifies it’s a NetworkPolicy.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**metadata:** Includes information like the name and namespace of the policy.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**spec:** 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**podSelector:** Defines the pods to which the policy applies based on labels. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**policyTypes:** Defines the direction of traffic (Ingress, Egress, or both). 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ingress and egress rules:** Define specific rules for incoming and outgoing traffic. 

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: example-network-policy
  namespace: default
spec:
  # Pod selector to define the target pods for this policy
  podSelector:
    matchLabels:
      app: my-app
  # Define the direction of traffic
  policyTypes:
  - Ingress
  - Egress
  # Ingress rules specify allowed incoming connections
  ingress:
  - from:
    - podSelector:          # Allows traffic from specific pods
        matchLabels:
          app: allowed-app
    - namespaceSelector:    # Allows traffic from pods in certain namespaces
        matchLabels:
          name: trusted-namespace
    - ipBlock:              # Allows traffic from specific IP ranges
        cidr: 192.168.1.0/24
        except:
        - 192.168.1.5/32
    ports:                  # Specifies the allowed ports for incoming traffic
    - protocol: TCP
      port: 8080
  # Egress rules specify allowed outgoing connections
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: allowed-external-app
    - namespaceSelector:
        matchLabels:
          name: external-namespace
    - ipBlock:
        cidr: 10.0.0.0/16
    ports:
    - protocol: TCP
      port: 80
```

## Key Components:

**1. Pod Selector:** This field selects the pods that the network policy applies to based on labels. If empty, the policy applies to all pods in the namespace. 

**2. Policy Types:**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Ingress:** Defines incoming traffic rules. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Egress:** Defines outgoing traffic rules. 

**3. Ingress Rules:**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**from:** Specifies which sources are allowed to connect to the pods. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**podSelector:** Defines pods by labels that can communicate with the selected pod(s). 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**namespaceSelector:** Allows traffic from pods in a particular namespace. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ipBlock:** Allows traffic from specific IP ranges or CIDR blocks. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ports:** Specifies which ports and protocols are allowed.

**4. Egress Rules:** 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**to:** Specifies which destinations the selected pod(s) can connect to. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Similar to from in ingress rules, with podSelector, namespaceSelector, and ipBlock. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ports:** Specifies allowed ports and protocols for outgoing connections. 

## Use Cases:

**Isolation of namespaces:** Use network policies to restrict communication between namespaces for security.

**Microservices access control:** Define rules for which services can communicate, only allowing necessary connections.

**Public and private access:** Block all external traffic to sensitive services while allowing only internal traffic.

## Default Behavior:

If no NetworkPolicy is applied in a namespace, the default behavior is to allow all traffic both for ingress (incoming) and egress (outgoing) communication. This means that:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**1-** Pods can communicate with each other freely within the same namespace or across different namespaces.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**2-** All external traffic is allowed to reach the pods (assuming appropriate services and routes are configured).
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**3-** All egress traffic from the pods is allowed to reach external services or other pods.