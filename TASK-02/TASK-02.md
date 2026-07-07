# 🚀 Task-02: Implement the Native Sidecar Pattern Using `emptyDir` Volume

## 🎯 Objective

The objective of this task is to implement the **Native Sidecar Pattern** in Kubernetes by deploying an **NGINX web server** alongside a **Sidecar Container** that continuously reads the NGINX access and error logs from a shared `emptyDir` volume.

This task demonstrates how Kubernetes **Native Sidecar Containers** (introduced in Kubernetes **v1.29+**) are created using an **`initContainer`** with **`restartPolicy: Always`**.

> 💡 **Real-world Scenario:** Native Sidecars are commonly used for log shipping (Fluent Bit/Fluentd), monitoring (Prometheus Exporters), service mesh (Istio Envoy), and security agents.

---

# 🏗️ Environment Setup

Verify cluster connectivity before starting.

```bash
kubectl get nodes
kubectl get pods
```

---

# 📝 Solution

Create **webserver.yaml**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webserver

spec:
  volumes:
    - name: shared-logs
      emptyDir: {}

  initContainers:
    - name: sidecar-container
      image: ubuntu:latest
      restartPolicy: Always
      command:
        - sh
        - -c
        - while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx

  containers:
    - name: nginx-container
      image: nginx:latest
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
```

Deploy the Pod.

```bash
kubectl apply -f webserver.yaml
```

---

# ✅ Verification

### Verify Pod Status

```bash
kubectl get pods
```

Expected Output

```text
NAME        READY   STATUS
webserver   2/2     Running
```

---

### Verify Pod Details

```bash
kubectl describe pod webserver
```

---

### Verify Sidecar Logs

```bash
kubectl logs webserver -c sidecar-container
```

---

### Verify NGINX Logs

```bash
kubectl logs webserver -c nginx-container
```

---

# 🛠️ Troubleshooting Commands

## 📌 Check Pod Status

```bash
kubectl get pods
```

---

## 📌 Describe Pod

```bash
kubectl describe pod webserver
```

---

## 📌 View Complete Pod YAML

```bash
kubectl get pod webserver -o yaml
```

---

## 📌 Check Regular Containers

```bash
kubectl get pod webserver -o jsonpath='{.spec.containers[*].name}'
```

Expected Output

```text
nginx-container
```

---

## 📌 Check Init Containers (Native Sidecar)

```bash
kubectl get pod webserver -o jsonpath='{.spec.initContainers[*].name}'
```

Expected Output

```text
sidecar-container
```

---
## 🛠️ Check Running Status of Each Container

kubectl get pod webserver -o jsonpath='{.status.containerStatuses[*].name}' echo

kubectl get pod webserver -o jsonpath='{.status.containerStatuses[*].ready}'

## 📊 Check Init Containers (if any)

kubectl get pod webserver -o jsonpath='{.spec.initContainers[*].name}'

To check their images: kubectl get pod webserver -o jsonpath='{.spec.initContainers[*].image}'

## 📝 Show Both Regular and Init Containers

kubectl get pod webserver -o jsonpath='Containers: {.spec.containers[*].name}{"\n"}Init Containers: {.spec.initContainers[*].name}'

---
---

## 📌 Check Images Used

### Regular Container

```bash
kubectl get pod webserver -o jsonpath='{.spec.containers[*].image}'
```

Expected Output

```text
nginx:latest
```

### Native Sidecar

```bash
kubectl get pod webserver -o jsonpath='{.spec.initContainers[*].image}'
```

Expected Output

```text
ubuntu:latest
```

---

## 📌 Check Container Status

```bash
kubectl describe pod webserver
```

---

## 📌 View Sidecar Logs

```bash
kubectl logs webserver -c sidecar-container
```

---

## 📌 View NGINX Logs

```bash
kubectl logs webserver -c nginx-container
```

---

## 📌 Execute Inside NGINX Container

```bash
kubectl exec -it webserver -c nginx-container -- /bin/bash
```

---

## 📌 Execute Inside Sidecar Container

```bash
kubectl exec -it webserver -c sidecar-container -- /bin/bash
```

---

## 📌 Verify Shared Volume

```bash
kubectl describe pod webserver | grep -A5 Mounts
```

---

## 📌 Validate YAML

```bash
kubectl apply --dry-run=client -f webserver.yaml
```

or

```bash
kubectl create --dry-run=client -f webserver.yaml
```

---

## 📌 Delete & Recreate Pod

```bash
kubectl delete pod webserver
kubectl apply -f webserver.yaml
```

---

# ❌ Errors Encountered

## 🔴 Error 1: Pod Stuck in Init State

```text
webserver   0/1   Init:0/1
```

### Cause

Initially, the sidecar container was created as a traditional init container without the **Native Sidecar** configuration. Since the command contained an infinite loop, the init container never completed, preventing the NGINX container from starting.

### Resolution

Configured the sidecar as a **Native Sidecar Container** by adding:

```yaml
restartPolicy: Always
```

under the `initContainers` section.

---

## 🔴 Error 2: Log Files Not Found

```text
cat: /var/log/nginx/access.log: No such file or directory
cat: /var/log/nginx/error.log: No such file or directory
```

### Cause

The NGINX container never started because the init container did not complete.

### Resolution

Implemented the Native Sidecar configuration using `restartPolicy: Always`, allowing both containers to run together.

---

## 🔴 Error 3: YAML Parsing Error

```text
error converting YAML to JSON:
yaml: line XX: did not find expected key
```

### Cause

* Incorrect indentation of `command`
* Incorrect indentation under `emptyDir`
* Included an unnecessary `status: {}` section

### Resolution

Corrected the YAML indentation and removed the `status` field.

---

## 🔴 Error 4: Validator Failure

```text
'sidecar-container' doesn't exist

Image used is not 'ubuntu:latest'
```

### Cause

Initially, the sidecar was created as a regular container. However, the KodeKloud Engineer validator expected a **Native Sidecar Container** under the `initContainers` section with:

```yaml
restartPolicy: Always
```

### Resolution

Moved the sidecar to the `initContainers` section and added `restartPolicy: Always`. The validator then passed successfully.

---

# 🧠 Architecture

```text
                     Kubernetes Pod
       ┌──────────────────────────────────────┐
       │                                      │
       │        📂 shared-logs (emptyDir)     │
       │                                      │
       │   ┌──────────────────────────────┐   │
       │   │ Native Sidecar Container     │   │
       │   │ (ubuntu:latest)              │   │
       │   │ restartPolicy: Always        │   │
       │   │ Reads & Ships Logs           │   │
       │   └──────────────▲───────────────┘   │
       │                  │                   │
       │                  │ Shared Volume     │
       │                  ▼                   │
       │   ┌──────────────────────────────┐   │
       │   │ nginx-container              │   │
       │   │ nginx:latest                 │   │
       │   │ Writes Access/Error Logs     │   │
       │   └──────────────────────────────┘   │
       │                                      │
       └──────────────────────────────────────┘
```

---

# 🎤 Interview Questions

### ❓1. What is a Native Sidecar Container?

A Native Sidecar Container is an init container configured with `restartPolicy: Always`. It starts before the application container and continues running throughout the Pod lifecycle.

---

### ❓2. What is the purpose of `restartPolicy: Always` in an init container?

It converts the init container into a Native Sidecar, allowing it to continue running after the application starts.

---

### ❓3. What is an `emptyDir` volume?

An `emptyDir` is a temporary volume created when a Pod starts and deleted when the Pod is removed. It enables containers in the same Pod to share data.

---

### ❓4. Difference Between Traditional Init Container and Native Sidecar?

| Traditional Init Container | Native Sidecar Container              |
| -------------------------- | ------------------------------------- |
| Runs once                  | Runs continuously                     |
| Exits before app starts    | Continues after app starts            |
| Used for initialization    | Used for logging, monitoring, proxies |

---

### ❓5. How do you check Native Sidecar containers?

```bash
kubectl get pod webserver -o jsonpath='{.spec.initContainers[*].name}'
```

---

### ❓6. How do you check the image used by the Native Sidecar?

```bash
kubectl get pod webserver -o jsonpath='{.spec.initContainers[*].image}'
```

---

# 🌍 Real-Time DevOps Use Cases

* 📜 Fluent Bit / Fluentd log shippers
* 📊 Prometheus Exporters
* 🌐 Istio Envoy Proxy
* 🔐 Security Agents
* 🔄 Backup & File Synchronization
* 📈 OpenTelemetry Collectors

---

# 📚 Key Takeaways

* 🚀 Implemented the **Kubernetes Native Sidecar Container** pattern.
* 📁 Used an **`emptyDir`** volume for shared log storage.
* 🔄 Learned the difference between **traditional init containers** and **Native Sidecar Containers**.
* 🛠️ Practiced debugging validator issues, YAML syntax, Pod initialization, and container configuration.
* 📖 Learned how to inspect both **regular containers** and **initContainers** using `kubectl`.
* 💼 Gained hands-on experience with a **Kubernetes v1.29+ feature** that is increasingly adopted in modern production environments.
