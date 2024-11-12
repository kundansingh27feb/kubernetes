# ReplicaSet

ReplicaSet is a controller that manages and ensures the availability of a specified number of replicas of a particular pod. It helps in achieving high availability by automatically creating or deleting pod replicas as needed to maintain the desired count. This ensures that the application can handle loads and maintain service continuity in case of individual pod failures.  

## Key Aspects of a ReplicaSet:

**Desired Replicas:** The number of pod instances you want running. 

**Selector:** Defines which pods the ReplicaSet should manage based on labels. 

**Template:** Defines the pod specifications, including the container image and other configurations like resource limits. 

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replica
  labels:
    app: nginx
spec:
  replicas: 5
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: mynginx-pod
        image: nginx:latest
        ports:
        - containerPort: 80
```

`kubectl apply -f replicaset.yaml`

`kubectl get replicaset`

`kubectl get pods -l app=my-app`

`kubectl get pods –show-label`

# MatchLabels And MatchExpressions

In Kubernetes, matchLabels and matchExpressions are used within selectors to define the criteria for selecting resources, such as pods, that should be managed by other resources like ReplicaSets, Deployments, and Services. These selectors help Kubernetes controllers identify and group resources based on specific labels.

## 1. matchLabels

**Syntax:** A map of key-value pairs.

**Usage:** matchLabels is a simple and direct way to select resources that contain specific labels. Each label must match exactly for a pod to be selected.

**Example:** If you specify matchLabels: { "app": "my-app" }, it will select all pods with a label app=my-app.

```
selector:
  matchLabels:
    app: my-app
```

In this case, all pods with the label app=my-app will be managed by the resource (e.g., a ReplicaSet or Deployment) that defines this selector. 

## 2. matchExpressions 

**Syntax:** An array of expressions, each with key, operator, and values. 

**Usage:** matchExpressions allows for more complex selection criteria than matchLabels. With matchExpressions, you can use operators like:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**In:** Selects resources with label values that match any of the listed values. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**NotIn:** Selects resources without label values matching the listed values. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Exists:** Selects resources that have a specified label key, regardless of the value. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**DoesNotExist:** Selects resources that do not have a specified label key. 


```
selector:
  matchExpressions:
    - key: app
      operator: In
      values: ["my-app", "other-app"]
    - key: environment
      operator: NotIn
      values: ["test"]
```

In this case:

It selects resources where the app label is either my-app or other-app.

It excludes resources with the environment label set to test.