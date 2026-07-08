# 🚀 Task-05: Perform Deployment Rolling Update and Rollback in Kubernetes

## 🎯 Objective

The objective of this task is to deploy an Apache HTTP Server using a **Deployment**, expose it using a **NodePort Service**, perform a **Rolling Update** to a newer image version, and finally **rollback** to the previous version.

This task demonstrates Kubernetes' **zero-downtime deployment strategy** and its ability to **rollback** to a previous stable release if an upgrade fails.

> 💡 **Real-world Scenario:** Rolling Updates and Rollbacks are critical in production environments to ensure high availability and minimize downtime during application upgrades.

---

# 🏗️ Environment Setup

Verify the Kubernetes cluster before starting.

```bash
kubectl get nodes
kubectl get ns
kubectl get deployments --all-namespaces
kubectl get svc --all-namespaces
```

---

# 📝 Solution

## Step 1️⃣ Create Namespace

```bash
kubectl create namespace nautilus
```

Verify:

```bash
kubectl get ns
```

---

## Step 2️⃣ Create Deployment

Create **httpd-deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deploy
  namespace: nautilus

spec:
  replicas: 4

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 2

  selector:
    matchLabels:
      app: httpd

  template:
    metadata:
      labels:
        app: httpd

    spec:
      containers:
        - name: httpd
          image: httpd:2.4.27
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: httpd-service
  namespace: nautilus

spec:
  type: NodePort

  selector:
    app: httpd

  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```

Apply the manifest.

```bash
kubectl apply -f httpd-deployment.yaml
```

---

## Step 3️⃣ Verify Deployment

```bash
kubectl get deployment -n nautilus
```

Expected Output

```text
NAME            READY   UP-TO-DATE   AVAILABLE
httpd-deploy    4/4     4            4
```

---

## Step 4️⃣ Verify Pods

```bash
kubectl get pods -n nautilus -o wide
```

---

## Step 5️⃣ Verify Service

```bash
kubectl get svc -n nautilus
```

Expected Output

```text
NAME             TYPE       CLUSTER-IP      PORT(S)
httpd-service    NodePort   10.xx.xx.xx     80:30008/TCP
```

---

# 🔄 Perform Rolling Update

Update the image.

```bash
kubectl set image deployment/httpd-deploy \
httpd=httpd:2.4.43 \
-n nautilus
```

---

## Verify Rollout Status

```bash
kubectl rollout status deployment/httpd-deploy -n nautilus
```

---

## Verify Updated Image

```bash
kubectl describe deployment httpd-deploy -n nautilus
```

or

```bash
kubectl get deployment httpd-deploy -n nautilus -o yaml
```

---

# ⏪ Rollback Deployment

Undo the latest rollout.

```bash
kubectl rollout undo deployment/httpd-deploy -n nautilus
```

---

## Verify Rollback

```bash
kubectl rollout status deployment/httpd-deploy -n nautilus
```

---

## Verify Original Image

```bash
kubectl describe deployment httpd-deploy -n nautilus
```

Expected Image

```text
httpd:2.4.27
```

---

# 🛠️ Troubleshooting Commands

## Check Namespace

```bash
kubectl get ns
```

---

## Check Deployment

```bash
kubectl get deployment -n nautilus
```

---

## Describe Deployment

```bash
kubectl describe deployment httpd-deploy -n nautilus
```

---

## Check ReplicaSets

```bash
kubectl get rs -n nautilus
```

---

## Describe ReplicaSets

```bash
kubectl describe rs -n nautilus
```

---

## Check Pods

```bash
kubectl get pods -n nautilus -o wide
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name> -n nautilus
```

---

## Check Services

```bash
kubectl get svc -n nautilus
```

---

## Describe Service

```bash
kubectl describe svc httpd-service -n nautilus
```

---

## Verify Rollout Status

```bash
kubectl rollout status deployment/httpd-deploy -n nautilus
```

---

## View Rollout History

```bash
kubectl rollout history deployment/httpd-deploy -n nautilus
```

Expected Output

```text
REVISION
1
2
```

---

## View Revision Details

```bash
kubectl rollout history deployment/httpd-deploy \
--revision=1 \
-n nautilus
```

---

## Check Current Image

```bash
kubectl get deployment httpd-deploy \
-n nautilus \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

---

## Rollback Deployment

```bash
kubectl rollout undo deployment/httpd-deploy -n nautilus
```

---

## Rollback to Specific Revision

```bash
kubectl rollout undo deployment/httpd-deploy \
--to-revision=1 \
-n nautilus
```

---

# ❌ Errors Encountered

> **No errors encountered during this task.**
>
> *(Update this section only if you encounter any issues while completing the lab.)*

---

# 🧠 Architecture

```text
                    Kubernetes Cluster

                    Namespace: nautilus
                           │
                           ▼
                 Deployment (4 Replicas)
                 httpd-deploy
                           │
                    RollingUpdate
        maxSurge = 1 | maxUnavailable = 2
                           │
          ┌──────────┬──────────┬──────────┬──────────┐
          ▼          ▼          ▼          ▼
       Pod-1      Pod-2      Pod-3      Pod-4
     httpd:2.4.27 httpd:2.4.27 httpd:2.4.27 httpd:2.4.27
                           │
                           ▼
                 NodePort Service
                 httpd-service
                 Port 80 → 30008
```

---

# 🎤 Interview Questions

### ❓1. What is a Rolling Update?

A Rolling Update gradually replaces old Pods with new Pods without bringing down the entire application, ensuring minimal or zero downtime.

---

### ❓2. What is `maxSurge`?

`maxSurge` specifies the maximum number of additional Pods that can be created during an update.

Example:

```yaml
maxSurge: 1
```

With 4 replicas:

* Desired Pods = 4
* Maximum Pods during update = **5**

---

### ❓3. What is `maxUnavailable`?

`maxUnavailable` specifies the maximum number of Pods that can be unavailable during the update.

Example:

```yaml
maxUnavailable: 2
```

Kubernetes ensures at least **2 Pods remain available** during the rollout.

---

### ❓4. What happens during a Rolling Update?

1. A new Pod is created.
2. One old Pod is terminated.
3. Health checks are performed.
4. The process repeats until all Pods use the new image.

---

### ❓5. What is Rollback?

Rollback restores the previous Deployment revision if a newly deployed version has issues.

---

### ❓6. How do you check Deployment revisions?

```bash
kubectl rollout history deployment/httpd-deploy -n nautilus
```

---

### ❓7. How do you rollback to the previous version?

```bash
kubectl rollout undo deployment/httpd-deploy -n nautilus
```

---

### ❓8. Why shouldn't you use the wrong image even temporarily?

Each Deployment change creates a new **revision**. Using an incorrect image and then correcting it creates additional revisions, which can break rollback history and, in labs like this one, cause validation failures.

---

# 🌍 Real-Time DevOps Use Cases

* 🚀 Zero-downtime application upgrades.
* 🔄 Safe production deployments.
* 📈 Continuous Delivery (CD) pipelines.
* 🛡️ Immediate rollback during failed releases.
* ⚖️ High availability during software updates.
* ☁️ Kubernetes production application lifecycle management.

---

# 📚 Key Takeaways

* 🚀 Created a Deployment with **4 replicas** in a dedicated namespace.
* 🌐 Exposed the application using a **NodePort Service**.
* 🔄 Performed a **Rolling Update** from `httpd:2.4.27` to `httpd:2.4.43`.
* ⏪ Successfully rolled back to the previous stable version.
* 📖 Learned how Kubernetes maintains **Deployment revision history**.
* 🛠️ Practiced rollout monitoring, rollback, ReplicaSet inspection, and Deployment troubleshooting.
* 💼 Gained hands-on experience with one of the most important production deployment strategies used in modern DevOps environments.
