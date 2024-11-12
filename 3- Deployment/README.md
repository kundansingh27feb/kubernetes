# Kubernetes Deployment 
<hr>
Kubernetes Deployment is an abstraction that provides declarative updates to applications. It manages the deployment of pods and ensures that the specified number of pods are running at all times.

## Deployments offer several features:

**Scaling:** Easily scale your application up or down by adjusting the number of replicas.

**Rolling Updates:** Deploy new versions of an application incrementally to avoid downtime.

**Rollback:** If a deployment update fails, it can be rolled back to the previous stable version.

**Self-healing:** Automatically replaces failed or unresponsive pods to maintain the desired state.

## Key Concepts:

**Replica Sets:** Ensures a specified number of pod replicas are running.

**Rolling Updates:** Gradually updates pods with new versions to ensure minimal disruption.

**Rollbacks:** Revert to a previous deployment state if an update fails.
<hr>

# Deploy, Upgrade And Rollout Steps to Apply and Manage the Deployment

<hr>

## 1. Apply the Deployment

Save the above YAML to a file (e.g., nginx-deployment.yaml) and apply it:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`kubectl apply -f nginx-deployment.yaml`

## 2. Update the Deployment

To update the Deployment, for example to a new version of the Nginx image, modify the image field in the YAML file:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`kubectl edit deployment nginx-deployment`

```
spec:
  containers:
  - name: nginx
    image: nginx:1.21.7
```

Save the YAML.

## 3. Rollback the Deployment

If you need to roll back to the previous version, you can use the `kubectl rollout` command. First, check the rollout history:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`kubectl rollout history deployment/nginx-deployment`

You will see the list of revisions. To roll back to a previous revision, use: 

`kubectl rollout undo deployment/nginx-deployment --to-revision=<revision_number>`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`kubectl rollout undo deployment/nginx-deployment --to-revision=1`  # Rollout To Particular Version

If you want to roll back to the previous state without specifying a revision:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`kubectl rollout undo deployment/nginx-deployment`

## 4. Check Deployment Status

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`kubectl get deployments`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`kubectl get pods`

## 5. View Rollout Status

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`kubectl rollout status deployment/nginx-deployment`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`kubectl describe deployment nginx-deployment`

<hr>

By default, Kubernetes maintains the following settings during the rolling update:

**maxUnavailable:** The maximum number of pods that can be unavailable during the update process. The default is 25% of the total number of pods. 

**maxSurge:** The maximum number of extra pods that can be created during the update process. The default is 25% of the total number of pods. 


For a deployment with 2 replicas, the default behavior would mean: 

**maxUnavailable:** 1 pod (25% of 2 replicas). 

**maxSurge:** 1 additional pod (25% of 2 replicas).

This means that during the upgrade, at most 1 pod will be unavailable at any given time, ensuring that there is always at least 1 pod running.

<hr>

# Key Differences Between ReplicaSet And Deployment 

<hr> 

## Level of Abstraction: 

**ReplicaSet:** Manages a set of pod replicas. 

**Deployment:** Manages ReplicaSets and provides higher-level features for managing applications. 

## Updates and Rollbacks: 

**ReplicaSet:** Does not natively support rolling updates or rollbacks. 

**Deployment:** Provides declarative updates, rolling updates, and rollback capabilities. 

## Self-Healing and Scaling: 

**ReplicaSet:** Ensures the specified number of pod replicas are running but lacks higher-level management features. 

**Deployment:** Manages the desired state of applications, including self-healing and scaling. 

<hr>

# When to Use What? 

<hr>

**Use Deployments:** For most use cases, including when you need rolling updates, rollbacks, and more sophisticated application lifecycle management. 

**Use ReplicaSets:** Rarely used directly but can be useful if you need a simpler form of replication without the additional features of Deployments.

# Deployment Update types? 

<hr>

**RollingUpdate:** Commonly used for applications requiring high availability during updates.

**Recreate:** Might be used for simpler applications, where downtime is acceptable or for applications that do not support concurrent version runs.

