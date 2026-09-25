# Class 49 - Kubernetes Storage

class url: https://youtu.be/LhtpXpvU4d0
## EmptyDir

* `emptyDir` is a temporary storage volume created when a Pod is assigned to a Node.
* Multiple containers in the same Pod can use `emptyDir` to share files.
* It can also be used to store temporary data.
* If a container crashes, the `emptyDir` data remains.
* When the Pod is deleted or recreated on another Node, the `emptyDir` data is deleted.

### Example

```yaml
volumes:
  - name: shared-data
    emptyDir: {}

containers:
  - name: container1
    volumeMounts:
      - name: shared-data
        mountPath: /data

  - name: container2
    volumeMounts:
      - name: shared-data
        mountPath: /data
```

---

# Persistent Volume (PV)

* PV is a **cluster-level storage resource**.
* It represents storage that is available for Pods to use.
* PV is **not namespace-specific**.
* A PV can be created manually or dynamically using a `StorageClass`.

## Access Modes

| Mode   | Meaning                                        |
| ------ | ---------------------------------------------- |
| `RWO`  | ReadWriteOnce – read/write from one Node       |
| `ROX`  | ReadOnlyMany – read-only from multiple Nodes   |
| `RWX`  | ReadWriteMany – read/write from multiple Nodes |
| `RWOP` | ReadWriteOncePod – read/write by only one Pod  |

### RWO

* Volume can be mounted as read/write by one Node.
* Multiple Pods on the **same Node** can access the volume.

### RWX

* Multiple Nodes can mount the volume and read/write.

### RWOP

* Only **one Pod in the entire cluster** can mount the volume as read/write.

---

# PV Reclaim Policy

The reclaim policy defines what happens to the PV/storage after the PVC is deleted.

### Retain

* PV is kept after PVC deletion.
* PV becomes `Released`.
* Administrator must clean it up manually.

### Delete

* PV and the associated storage are deleted automatically.

### Recycle

* Old reclaim policy.
* **Deprecated**.
* Dynamic provisioning is recommended instead.

```yaml
persistentVolumeReclaimPolicy: Retain
```

---

# PV Protection

Kubernetes provides **PV protection** by default.

* `kubernetes.io/pv-protection` finalizer protects the PV.
* A PV cannot be deleted while it is still bound to a PVC.
* This helps prevent accidental deletion of storage that is currently in use.

---

# Persistent Volume Claim (PVC)

* PVC is a **request for storage** made by a user/application.
* PVC specifies requirements such as:

  * Storage size
  * Access mode
  * StorageClass

Example:

```yaml
resources:
  requests:
    storage: 10Gi

accessModes:
  - ReadWriteOnce
```

* Kubernetes looks for a suitable PV for the PVC.
* If a matching PV is found, the PV and PVC become `Bound`.

---

# Static vs Dynamic Provisioning

## Static Provisioning

```text
Admin creates PV
       ↓
User creates PVC
       ↓
PVC searches for matching PV
       ↓
PV + PVC → Bound
```

* Administrator creates PVs beforehand.
* PVC claims an existing PV.

## Dynamic Provisioning

```text
User creates PVC
       ↓
StorageClass
       ↓
Provisioner
       ↓
Storage is created automatically
       ↓
PV is created
       ↓
PVC + PV → Bound
```

* PV is automatically created when required.
* Example provisioners:

  * AWS EBS
  * GCE Persistent Disk
  * Azure Disk

---

# PVC-PV Claim Process

### 1. Create PV

PV defines:

* Storage capacity
* Access mode
* StorageClass
* Reclaim policy

### 2. Create PVC

PVC requests:

* Storage size
* Access mode
* StorageClass

### 3. PVC Validation

Kubernetes checks:

* PVC configuration
* StorageClass
* Storage requirements

### 4. PV Matching

Kubernetes searches for a suitable PV.

For static provisioning:

```text
Available PV → Matching PVC → Bound
```

For dynamic provisioning:

```text
No matching PV
      ↓
StorageClass provisioner
      ↓
New storage + PV
      ↓
PVC → Bound
```

### 5. PVC-PV Binding

```text
PVC ↔ PV
```

Both become:

```text
STATUS: Bound
```

### 6. Pod Uses PVC

The Pod references the PVC:

```yaml
volumes:
  - name: app-storage
    persistentVolumeClaim:
      claimName: my-pvc
```

The volume is mounted inside the container.

---

# Pod Scheduling and Mounting

When a Pod uses a PVC:

```text
Pod
 ↓
PVC
 ↓
PV
 ↓
Physical/Cloud Storage
```

For example, with AWS EBS:

```text
Pod
 ↓
PVC
 ↓
PV
 ↓
EBS Volume
 ↓
EC2 Worker Node
```

Kubernetes schedules the Pod to a suitable Node and the storage is attached/mounted as required.

---

# Data Persistence

Unlike `emptyDir`, data in a PV normally survives Pod deletion.

```text
Pod deleted
     ↓
PVC remains
     ↓
PV remains
     ↓
Data remains
```

A new Pod can use the same PVC and access the stored data, subject to the volume's access mode and storage capabilities.

---

# PVC Deletion

When a PVC is deleted, the result depends on the PV's reclaim policy.

### Retain

```text
PVC deleted
     ↓
PV → Released
     ↓
Storage remains
```

Manual cleanup is required.

### Delete

```text
PVC deleted
     ↓
PV deleted
     ↓
Underlying storage deleted
```

---

# Storage Flow

```text
StorageClass
     ↓
Provisioner
     ↓
     PV
     ↑
     ↓
    PVC
     ↓
    Pod
     ↓
 Container
     ↓
 Application
```

## Important

* **emptyDir** → Temporary storage, deleted with the Pod.
* **PV** → Cluster-level persistent storage.
* **PVC** → Request/claim for storage.
* **StorageClass** → Defines how storage can be dynamically provisioned.
* **RWO** → Read/write from one Node.
* **RWX** → Read/write from multiple Nodes.
* **RWOP** → Read/write by one Pod.
* **Retain** → Keep storage after PVC deletion.
* **Delete** → Delete storage after PVC deletion.
