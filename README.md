# kubernetes
Kubernetes: Basics To Advance with AKS(Azure Kubernetes Services)

# Kubernetes Architecture

A Kubernetes cluster consists of a set of worker machines, called nodes, that run containerized applications. Every cluster has at least one worker node. 

In production environments, the control plane usually runs across multiple computers and a cluster usually runs multiple nodes, providing fault-tolerance and high availability. 

# 1. Control Plane Components

The control plane's components make global decisions about the cluster (for example, scheduling), as well as detecting and responding to cluster events (for example, starting up a new pod when a Deployment's replicas field is unsatisfied). 

##   A. kube-apiserver:

**Purpose:** The API server is the central management entity that exposes the Kubernetes API. It is the front-end for the Kubernetes control plane. 

**Functionality:** It handles all the internal and external REST API requests, including those from the kubectl command-line interface. It validates and configures the data for API objects, which include pods, services, replication controllers, and others. 

**Data Stores:** The kube-apiserver relies on etcd as the backend data store where the state of the cluster is persisted. 

##   B. etcd:

**Purpose:** etcd is a distributed key-value store used to store all cluster data. 

**Functionality:** It stores the persistent state of all cluster components and their configurations. etcd is critical for Kubernetes to manage configuration data, service discovery, and maintaining the desired state of the cluster. 

**Data Stores:** Itself is a distributed key-value store. It stores all the persistent data of the cluster. 

##   C. kube-scheduler:

**Purpose:** The scheduler is responsible for distributing workloads across the cluster. 

**Functionality:** It watches for newly created pods that do not have a node assigned yet and selects a node for them to run on based on factors like resource requirements, hardware/software/policy constraints, affinity and anti-affinity specifications, data locality, inter-workload interference, and deadlines. 

**Data Stores:** It uses the API server to get information about pods and nodes. 

##   D. kube-controller-manager:

**Purpose:** The controller manager runs controller processes that regulate the state of the cluster. 

**Functionality:** Controllers are responsible for noticing and responding when the actual state of the cluster does not match the desired state. Examples of controllers include: 

&nbsp;&nbsp;&nbsp;&nbsp;**<h3>Node Controller:</h3>** Responsible for noticing and responding when nodes go down. 

&nbsp;&nbsp;&nbsp;&nbsp;**<h3>Replication Controller:</h3>** Responsible for maintaining the correct number of pods for every replication controller object in the system. 

&nbsp;&nbsp;&nbsp;&nbsp;**<h3>Endpoints Controller:</h3>** Populates the Endpoints object (i.e., joins Services & Pods).

&nbsp;&nbsp;&nbsp;&nbsp;**<h3>Service Account & Token Controllers:</h3>** Create default accounts and API access tokens for new namespaces.

**Data Stores:** Controllers communicate with the API server to obtain the cluster state and make necessary adjustments to reach the desired state. 

##   E. cloud-controller-manager:

**Purpose:** The cloud-controller-manager allows Kubernetes to interact with the underlying cloud provider. 

**Functionality:** It includes cloud-specific control loops that integrate with the cloud provider’s API to manage cloud resources. It has controllers for managing nodes, routes, and load balancers. 

&nbsp;&nbsp;&nbsp;&nbsp;**<h3>Node Controller:</h3>** Checks the cloud provider to determine if a node has been deleted in the cloud after it stops responding. 

&nbsp;&nbsp;&nbsp;&nbsp;**<h3>Route Controller:</h3>** For setting up routes in the underlying cloud infrastructure. 

&nbsp;&nbsp;&nbsp;&nbsp;**<h3>Service Controller:</h3>** For creating, updating, and deleting cloud provider load balancers. 

**Data Stores:** It relies on the cloud provider’s API and the Kubernetes API server to obtain the cluster state and perform cloud-specific operations. 


# 2. Node Components

Run on every node, maintaining running pods and providing the Kubernetes runtime environment:

## **kubelet:** 

Ensures that Pods are running, including their containers.

## **kube-proxy:** 

Maintains network rules on nodes to implement Services.

## **Container runtime:** 

Software responsible for running containers.