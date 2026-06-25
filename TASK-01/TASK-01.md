# 🚀 Task-01: Share Data Between Multiple Containers Using `emptyDir` Volume

## 🎯 Objective

The objective of this task is to create a **multi-container Pod** where both containers share the same temporary storage using an **`emptyDir` volume**.

This demonstrates how multiple containers within the same Pod can communicate and exchange data through a shared filesystem.

---

## 🏗️ Environment Setup

Verify cluster connectivity before starting.

```bash
kubectl get nodes
kubectl get pods
```

---

## 📝 Solution

Create the following YAML file.

**`volume-share.yaml`**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-datacenter
spec:
  volumes:
    - name: volume-share
      emptyDir: {}

  containers:
    - name: volume-container-datacenter-1
      image: fedora:latest
      command: ["/bin/bash", "-c", "sleep infinity"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/ecommerce

    - name: volume-container-datacenter-2
      image: fedora:latest
      command: ["/bin/bash", "-c", "sleep infinity"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/apps
```

Apply the configuration:

```bash
kubectl apply -f volume-share.yaml
```

---

## 📂 Create the Test File

Exec into the first container.

```bash
kubectl exec -it volume-share-datacenter -c volume-container-datacenter-1 -- /bin/bash
```

Create the file.

```bash
echo "Welcome to xFusionCorp Industries" > /tmp/ecommerce/ecommerce.txt
```

Exit the container.

```bash
exit
```

---

## ✅ Verification

### Verify Pod Status

```bash
kubectl get pods
```

Expected Output

```text
volume-share-datacenter   2/2   Running
```

---

### Verify File from First Container

```bash
kubectl exec volume-share-datacenter -c volume-container-datacenter-1 -- cat /tmp/ecommerce/ecommerce.txt
```

Expected Output

```text
Welcome to xFusionCorp Industries
```

---

### Verify File from Second Container

```bash
kubectl exec volume-share-datacenter -c volume-container-datacenter-2 -- cat /tmp/apps/ecommerce.txt
```

Expected Output

```text
Welcome to xFusionCorp Industries
```

🎉 Since both containers mount the **same `emptyDir` volume**, the file is visible from both mount paths.

---

## 💡 How `emptyDir` Works

```
                Pod
        ┌───────────────────┐
        │                   │
        │   emptyDir Volume │
        │   (volume-share)  │
        │                   │
        └───────┬───────────┘
                │
      ┌─────────┴─────────┐
      │                   │
      ▼                   ▼
Container-1          Container-2
/tmp/ecommerce       /tmp/apps
      │                   │
      └────── Same Storage ──────► ecommerce.txt
```

---

## 🎤 Interview Questions

### ❓1. What is an `emptyDir` volume?

An `emptyDir` is a temporary volume that is created when a Pod starts and exists for the lifetime of that Pod. All containers in the Pod can share it.

---

### ❓2. When is an `emptyDir` volume deleted?

It is deleted when the **Pod is removed** from the node.

---

### ❓3. Can multiple containers access the same `emptyDir`?

✅ Yes. That's one of its primary use cases.

---

### ❓4. What are common use cases of `emptyDir`?

* Sharing files between containers
* Temporary caching
* Scratch space
* Log sharing between application and sidecar containers
* Intermediate data processing

---

### ❓5. Is `emptyDir` persistent?

❌ No.

Data survives **container restarts**, but it is **lost when the Pod is deleted or rescheduled**.

---

### ❓6. Difference between `emptyDir` and `PersistentVolume`?

| emptyDir                     | PersistentVolume                            |
| ---------------------------- | ------------------------------------------- |
| Temporary storage            | Persistent storage                          |
| Exists only while Pod exists | Independent of Pods                         |
| Node-local                   | Can be network-backed                       |
| Suitable for temporary files | Suitable for databases and application data |

---

## 🌍 Real-Time DevOps Use Cases

🔹 **Sidecar Logging**

* Application writes logs to a shared `emptyDir`.
* A logging sidecar (e.g., Fluent Bit or Filebeat) reads the logs and sends them to Elasticsearch or Splunk.

🔹 **NGINX + Application**

* An application generates static files.
* NGINX serves those files from the shared volume.

🔹 **CI/CD Pipelines**

* One container compiles code.
* Another container packages or uploads the generated artifacts using the shared workspace.

---

## 📚 Key Takeaways

* ✅ Learned how to create a **multi-container Pod**.
* 📁 Understood how **`emptyDir`** enables shared temporary storage.
* 🔄 Verified that data written by one container is immediately accessible to another.
* ⚠️ Remember that `emptyDir` data is **ephemeral** and is deleted when the Pod is removed.
* 🎯 This pattern is commonly used for **sidecar containers**, **log sharing**, and **temporary workspace sharing** in Kubernetes.
