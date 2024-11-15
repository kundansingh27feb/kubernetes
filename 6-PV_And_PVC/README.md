# PV And PVC

Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) are used to manage storage resources in a way that decouples the storage configuration from the pods using it. This ensures applications can use persistent storage that outlives the lifecycle of a pod.

## PV

A Persistent Volume (PV) is a piece of storage provisioned by an administrator or dynamically by a storage class. It is a cluster-wide resource that is independent of any pod and remains available even if the pod using it is deleted.

**Characteristics:**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Provisioned by an admin or automatically via a storage class.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Represents a physical or logical storage resource.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Can be backed by NFS, iSCSI, local disk, or other storage systems.

Think of a Persistent Volume (PV) as a storage space that your Kubernetes cluster can use. It's like a folder on your hard drive or a shared drive that is set up and made available for applications to store data.

**Example:** Imagine an NFS server in your office with a folder called /mnt/data where you store important files. A PV in Kubernetes points to that folder and says, "This storage is ready for use!"

# PVC

A Persistent Volume Claim (PVC) is a request for storage by a user. It specifies how much storage is needed and any additional attributes (like access modes). The PVC is bound to a PV that matches the request.

**Characteristics:**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Acts as a consumer-facing abstraction over PVs.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ensures the pod doesn't need to know the specifics of the storage backend.

A Persistent Volume Claim (PVC) is like asking for some of that storage. It's a request from your application saying, "I need 5GB of space to save my files."

**Example:** You, as a user, might ask your admin for a specific amount of storage from the shared drive, like 5GB. In Kubernetes, a PVC works the same way—it asks for storage space that matches your application's needs.

## How They Work Together

1. An administrator sets up the PV, which points to real storage (e.g., an NFS folder, a disk, or some other storage).

2. An application requests storage using a PVC, specifying how much space it needs and how it wants to use it.

3. Kubernetes matches the PVC with a suitable PV and connects them.

4. The application uses the PVC to read/write data to the underlying storage.

## Analogy

**Imagine this scenario:**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Your office has a shared storage room (like a PV).

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;You submit a request form (like a PVC) to ask for some storage space.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Once approved, you’re given access to a specific locker (the PV) to store your stuff.

## Example

**1. Create a PV:**

An administrator says, "Here’s 10GB of storage available on my NFS server.

Kubernetes makes it available as a Persistent Volume.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  nfs:
    path: /mnt/data
    server: 192.168.1.100 #IP address of NFS server
  persistentVolumeReclaimPolicy: Retain
```

**capacity:** Specifies 10Gi of storage.


**accessModes:** Access modes determine how a Persistent Volume (PV) can be accessed by pods. Think of it like deciding if a file or folder is read-only or writable, and whether multiple people can access it.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**1. ReadWriteMany:** Multiple pods can read from and write to this volume at the same time. Used when Applications need shared storage for collaboration.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**2. ReadWriteOnce:** Only one pod can read from and write to this volume at a time. Used when Applications need dedicated storage.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**3. ReadOnlyMany:** Multiple pods can read from this volume at the same time, but none can write to it. Used for Static content (e.g., configuration files or shared media that shouldn’t be changed).


**nfs:** Defines the NFS server and path.


**persistentVolumeReclaimPolicy:** The reclaim policy determines what happens to a Persistent Volume (PV) when its claim (PVC) is released or deleted.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**1. Retain:** The PV and its data are kept even after the PVC is deleted. Use When you want to manually handle and preserve data for later use.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**2. Recycle:** The PV is cleared (files are deleted) and made available for a new PVC. Used for Quick reuse of storage for non-critical or temporary data.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**3. Delete:** The PV and its data are completely removed when the PVC is deleted. Use When you want to ensure sensitive data is destroyed after use.

| Feature                  | What it Does                                                   | Example Use Case                         |
|--------------------------|----------------------------------------------------------------|------------------------------------------|
| ReadWriteOnce (RWO)      | Single pod reads/writes to the volume.                         | Databases like MySQL.                    |
| ReadWriteMany (RWX)      | Multiple pods can read/write to the volume.                    | Shared file storage for web applications.|
| ReadOnlyMany (ROX)       | Multiple pods can only read from the volume.                   | Static content like configuration files. |
| Retain (Reclaim Policy)  | Keeps the volume and data after the claim is released.         | Backup data that needs manual handling.  |
| Recycle (Reclaim Policy) | Deletes all data and makes the volume available for new claims.| Temporary test data.                     |
| Delete (Reclaim Policy)  | Deletes the volume and all its data when the claim is released.| Sensitive data like customer records.    |

**2. Create a PVC:**

An application says, "I need 5GB of storage to save my files."

Kubernetes creates a Persistent Volume Claim.

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

**3. Use the PVC in a Pod**

The application now uses the PVC to store data. The PVC connects to the PV, and the pod writes its data to the storage.

```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
      volumeMounts:
        - mountPath: "/usr/share/nginx/html" #Mounts the PVC (my-pvc) at /usr/share/nginx/html inside the container.
          name: my-storage
  volumes:                                   #Links the volume to the PVC.
    - name: my-storage
      persistentVolumeClaim:
        claimName: my-pvc
```

## Why is This Useful?

1. Data Safety: Your data stays safe even if the pod crashes or is replaced.

2. Shared Storage: Multiple applications can share the same storage.

3. Flexibility: Developers don’t need to know about the underlying storage; they just request what they need using PVCs.

<hr>