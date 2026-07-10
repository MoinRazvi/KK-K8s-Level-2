
# 🚀 Task-10: Troubleshoot and Fix a Redis Deployment

## 🎯 Objective

The objective of this task is to troubleshoot an existing **Redis Deployment** whose Pods are not in the **Running** state. The goal is to identify the root cause, fix the configuration, and restore the application.

> 💡 **Real-world Scenario:** DevOps engineers spend more time troubleshooting existing deployments than creating new ones. Common issues include incorrect images, invalid YAML, missing environment variables, resource constraints, and probe failures.

---

# 🛠️ Troubleshooting Workflow

> Follow this sequence whenever a Pod is not running.

## Step 1️⃣ Check Deployment

```bash
kubectl get deployment
```

or

```bash
kubectl get deploy
```

---

## Step 2️⃣ Check Pods

```bash
kubectl get pods
```

If Pods are not running, note the **STATUS**.

Example:

```text
ImagePullBackOff
CrashLoopBackOff
ErrImagePull
Pending
CreateContainerConfigError
RunContainerError
```

The status usually provides the first clue.

---

## Step 3️⃣ Describe the Pod ⭐

```bash
kubectl describe pod <pod-name>
```

or

```bash
kubectl describe deployment redis-deployment
```

Pay special attention to the **Events** section at the bottom.

Example:

```text
Events:
Failed to pull image
Back-off restarting container
Failed Scheduling
Liveness probe failed
```

---

## Step 4️⃣ Check Logs

```bash
kubectl logs <pod-name>
```

If there are multiple containers:

```bash
kubectl logs <pod-name> -c <container-name>
```

---

## Step 5️⃣ Check Deployment YAML

```bash
kubectl get deployment redis-deployment -o yaml
```

Look for:

* Wrong image
* Wrong command
* Wrong environment variables
* Wrong ports
* Wrong labels
* Wrong selectors

---

## Step 6️⃣ Verify Container Image

```bash
kubectl get deployment redis-deployment \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

---

## Step 7️⃣ Edit Deployment

```bash
kubectl edit deployment redis-deployment
```

or

```bash
kubectl set image deployment/redis-deployment \
<container-name>=redis:latest
```

---

## Step 8️⃣ Watch Rollout

```bash
kubectl rollout status deployment redis-deployment
```

---

## Step 9️⃣ Verify Pods

```bash
kubectl get pods -w
```

---

## Step 🔟 Verify Deployment

```bash
kubectl get deployment
```

Expected

```text
READY   UP-TO-DATE   AVAILABLE
1/1     1            1
```

---

# 🛠️ Useful Troubleshooting Commands

## Check Deployments

```bash
kubectl get deploy
```

---

## Check ReplicaSets

```bash
kubectl get rs
```

---

## Check Pods

```bash
kubectl get pods -o wide
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

## View Logs

```bash
kubectl logs <pod-name>
```

---

## Check Deployment YAML

```bash
kubectl get deployment redis-deployment -o yaml
```

---

## Edit Deployment

```bash
kubectl edit deployment redis-deployment
```

---

## Check Rollout

```bash
kubectl rollout status deployment redis-deployment
```

---

## Rollout History

```bash
kubectl rollout history deployment redis-deployment
```

---

## Restart Deployment

```bash
kubectl rollout restart deployment redis-deployment
```

---

## Delete Faulty Pod

```bash
kubectl delete pod <pod-name>
```

The Deployment automatically recreates it.

---

# ❌ Errors Encountered

> ⚠️ **Update this section only after identifying the actual issue.**
>
> KodeKloud Engineer intentionally introduces a fault (for example, an incorrect image, invalid command, or bad configuration). Document only the error you encountered and how you resolved it.

---

# 🧠 Troubleshooting Flow

```text
                🚨 Pod Not Running
                        │
                        ▼
              kubectl get pods
                        │
                        ▼
          Check Pod STATUS & Events
                        │
                        ▼
         kubectl describe pod <pod>
                        │
                        ▼
         kubectl logs <pod>
                        │
                        ▼
      Identify Root Cause
(Image, Port, Env, Probe, etc.)
                        │
                        ▼
          Fix Deployment
 (edit / set image / patch)
                        │
                        ▼
      kubectl rollout status
                        │
                        ▼
        Pod Running Successfully ✅
```

---

# 🎤 Interview Questions

### ❓1. What is the first command you run when a Pod is not running?

```bash
kubectl get pods
```

---

### ❓2. Which command shows the actual reason a Pod failed?

```bash
kubectl describe pod <pod-name>
```

The **Events** section is usually the most informative.

---

### ❓3. When do you use `kubectl logs`?

To inspect application or container runtime logs when a container starts but crashes or behaves unexpectedly.

---

### ❓4. How do you update an incorrect container image?

```bash
kubectl set image deployment/redis-deployment \
<container-name>=redis:latest
```

or

```bash
kubectl edit deployment redis-deployment
```

---

### ❓5. What is the difference between `kubectl describe` and `kubectl logs`?

| `kubectl describe`                                      | `kubectl logs`                              |
| ------------------------------------------------------- | ------------------------------------------- |
| Shows Pod events, scheduling, configuration, and status | Shows application output from the container |
| Used to diagnose Kubernetes-level issues                | Used to diagnose application-level issues   |

---

### ❓6. What happens if you delete a Pod managed by a Deployment?

The Deployment automatically creates a replacement Pod to maintain the desired replica count.

---

# 🌍 Real-Time DevOps Use Cases

* 🚨 Investigating production outages
* 🔍 Debugging failed deployments
* 🛠️ Fixing image and configuration issues
* 📈 Monitoring rollout health
* 🔄 Recovering applications with minimal downtime
* ☸️ Day-2 Kubernetes operations

---

# 📚 Key Takeaways

* 🚀 Follow a structured troubleshooting workflow instead of guessing.
* 🔍 Use `kubectl describe` to inspect Kubernetes events and configuration issues.
* 📜 Use `kubectl logs` to investigate application-level failures.
* 🛠️ Correct deployment issues using `kubectl edit` or `kubectl set image`.
* 🔄 Verify changes with `kubectl rollout status` and ensure Pods return to the **Running** state.
* 💼 This troubleshooting methodology applies to virtually all Kubernetes workloads in production.

> **📌 Once you identify the actual issue (for example, `ImagePullBackOff`, `CrashLoopBackOff`, wrong image, bad command, etc.), send me the output of `kubectl get pods` and `kubectl describe pod <pod-name>`. I'll help you document the exact error and resolution for your GitHub repository.**

---
### GIVEN THE FILE OF CONFIGMAP AND FOUND THE TYPOS LIKE (cofig, alpin)

Excellent! This is a classic Kubernetes troubleshooting scenario. From the `describe` output, we can identify **two configuration mistakes**.

---

# 🔍 Root Cause Analysis

## ❌ Error 1: ConfigMap Name is Incorrect ⭐ (Primary Issue)

From the Events section:

```text
FailedMount:
configmap "redis-cofig" not found
```

Notice the typo:

```text
redis-cofig
```

It should probably be:

```text
redis-config
```

or whatever ConfigMap actually exists.

### Verify

Run:

```bash
kubectl get configmap
```

If you see:

```text
NAME
redis-config
```

then the Deployment has a typo.

### Fix

Edit the Deployment:

```bash
kubectl edit deployment redis-deployment
```

Find:

```yaml
configMap:
  name: redis-cofig
```

Change it to:

```yaml
configMap:
  name: redis-config
```

Save and exit.

---

# ❌ Error 2: Wrong Redis Image (Secondary Issue)

Your Deployment shows:

```text
Image: redis:alpin
```

The correct image is:

```text
redis:alpine
```

The **"e"** is missing.

Once the ConfigMap issue is fixed, the Pod will likely move to:

```text
ErrImagePull
```

because `redis:alpin` doesn't exist.

### Verify

```bash
kubectl get deployment redis-deployment -o yaml
```

or

```bash
kubectl describe deployment redis-deployment
```

---

### Fix

Either edit the Deployment:

```bash
kubectl edit deployment redis-deployment
```

Replace:

```yaml
image: redis:alpin
```

with:

```yaml
image: redis:alpine
```

or use:

```bash
kubectl set image deployment/redis-deployment \
redis-container=redis:alpine
```

---

# 🔄 Verify

Watch the rollout:

```bash
kubectl rollout status deployment redis-deployment
```

Check Pods:

```bash
kubectl get pods -w
```

Expected:

```text
NAME                                READY   STATUS
redis-deployment-xxxxxxxxxx-xxxxx   1/1     Running
```

---

# 📚 Update Your GitHub Notes

## ❌ Errors Encountered

### 🔴 Error 1: ConfigMap Not Found

**Error**

```text
MountVolume.SetUp failed for volume "config":
configmap "redis-cofig" not found
```

**Cause**

The Deployment referenced a ConfigMap named **`redis-cofig`**, which contained a typo. Since the ConfigMap did not exist, Kubernetes could not mount the volume, leaving the Pod in the **Pending** state.

**Resolution**

Verified the available ConfigMaps:

```bash
kubectl get configmap
```

Corrected the Deployment to reference the proper ConfigMap name (`redis-config`) and redeployed the Pod.

---

### 🔴 Error 2: Incorrect Container Image

**Error**

```text
Image: redis:alpin
```

**Cause**

The Redis image tag was misspelled (`alpin` instead of `alpine`), which would cause an **ErrImagePull** after the ConfigMap issue was resolved.

**Resolution**

Updated the Deployment image to:

```yaml
image: redis:alpine
```

or

```bash
kubectl set image deployment/redis-deployment \
redis-container=redis:alpine
```

---

# 💡 Interview Tip

When troubleshooting Kubernetes Pods, always check in this order:

1. ✅ `kubectl get pods`
2. ✅ `kubectl describe pod <pod-name>` (**especially the Events section**)
3. ✅ `kubectl logs <pod-name>` (if the container starts)
4. ✅ `kubectl get deployment -o yaml`
5. ✅ Fix the root cause and verify with `kubectl rollout status`

In this task, the **Events** section immediately revealed the ConfigMap issue, showing why `kubectl describe` is often the most valuable first troubleshooting command.


---

Perfect. The Deployment YAML confirms **both issues**.

---

# 🔴 Issue 1: Wrong Redis Image

Current:

```yaml
image: redis:alpin
```

Correct:

```yaml
image: redis:alpine
```

The tag `alpin` doesn't exist.

---

# 🔴 Issue 2: Wrong ConfigMap Name

Current:

```yaml
configMap:
  name: redis-cofig
```

Correct:

```yaml
configMap:
  name: redis-config
```

Notice:

```
redis-cofig   ❌
redis-config  ✅
```

---

# First Verify the ConfigMap

Before editing, check which ConfigMap exists.

```bash
kubectl get configmap
```

You should see something similar to:

```text
NAME
redis-config
```

If the ConfigMap is indeed `redis-config`, then edit the deployment.

---

# Fix the Deployment

```bash
kubectl edit deployment redis-deployment
```

Update these two lines:

```yaml
containers:
- image: redis:alpine
```

and

```yaml
configMap:
  name: redis-config
```

Save and exit.

---

# Watch the Rollout

```bash
kubectl rollout status deployment redis-deployment
```

---

# Verify

```bash
kubectl get pods -w
```

Expected:

```text
NAME                                READY   STATUS
redis-deployment-xxxxxxx            1/1     Running
```

---

# 🛠️ Why Was the Pod in **Pending** Instead of **ImagePullBackOff**?

This is an excellent interview question.

Kubernetes initializes a Pod in stages:

```text
1️⃣ Schedule Pod to Node
          │
          ▼
2️⃣ Mount Volumes
          │
          ▼
3️⃣ Download Image
          │
          ▼
4️⃣ Start Container
```

Your Pod failed at **Step 2** because the ConfigMap couldn't be mounted:

```text
FailedMount:
configmap "redis-cofig" not found
```

Since Kubernetes never reached the image-pull stage, it didn't yet discover that:

```yaml
image: redis:alpin
```

was also incorrect.

Once you fix the ConfigMap, Kubernetes proceeds to Step 3. At that point, if the image is still `redis:alpin`, you'll see:

```text
ErrImagePull
```

or

```text
ImagePullBackOff
```

---

## 🧠 Troubleshooting Flow

```text
Pod Created
     │
     ▼
Schedule to Node ✅
     │
     ▼
Mount ConfigMap ❌
     │
 Pending
     │
(Fix ConfigMap)
     │
     ▼
Pull Image ❌
(redis:alpin)
     │
ErrImagePull
     │
(Fix Image)
     │
     ▼
Container Starts ✅
     │
Running
```

This task is a great example of why it's important to fix issues in the order Kubernetes encounters them—**the first error can mask later ones**.

---

### QUESTION (after updating the configmap do we have to restart the pods or any service to get the changes reflected)

Excellent question! This is one of the most common Kubernetes interview questions.

The answer is: **it depends on how the ConfigMap is being used.**

---

# Scenario 1️⃣ ConfigMap Mounted as a Volume

Example:

```yaml
volumes:
- name: config
  configMap:
    name: redis-config
```

and

```yaml
volumeMounts:
- name: config
  mountPath: /redis-master
```

### If you only update the **contents** of the ConfigMap:

```bash
kubectl edit configmap redis-config
```

or

```bash
kubectl apply -f configmap.yaml
```

Kubernetes **automatically updates the mounted files** inside the Pod (typically within a minute or so).

✅ **No Pod restart is required.**

---

# Scenario 2️⃣ ConfigMap Used as Environment Variables

Example:

```yaml
env:
- name: DB_HOST
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: db_host
```

If you change the ConfigMap:

```bash
kubectl edit configmap app-config
```

the running Pod **does not** get the updated environment variable.

You must restart the Pods.

Example:

```bash
kubectl rollout restart deployment my-app
```

or

```bash
kubectl delete pod <pod-name>
```

The Deployment will recreate it with the updated environment variables.

---

# Scenario 3️⃣ ConfigMap Name Was Incorrect (Your Redis Task)

In your task, the Deployment referenced:

```yaml
configMap:
  name: redis-cofig
```

which should have been:

```yaml
configMap:
  name: redis-config
```

Here, you are **changing the Deployment specification**, not the ConfigMap itself.

When you save the Deployment:

```bash
kubectl edit deployment redis-deployment
```

Kubernetes detects that the Pod template has changed and automatically performs a **rolling update**.

A new Pod is created with the corrected ConfigMap reference, and the old Pod is terminated.

✅ **No manual restart is required.**

You can verify the rollout with:

```bash
kubectl rollout status deployment redis-deployment
```

---

# Scenario 4️⃣ If You Edit the Pod Directly

If you somehow modify a standalone Pod (not managed by a Deployment), Kubernetes does not recreate it automatically.

In that case, you would need to delete and recreate the Pod.

---

# Summary Table

| Change Made                                        | Restart Required?                                  |
| -------------------------------------------------- | -------------------------------------------------- |
| Update ConfigMap mounted as a **volume**           | ❌ Usually no                                       |
| Update ConfigMap used as **environment variables** | ✅ Yes                                              |
| Fix ConfigMap **name** in a Deployment             | ❌ No (Deployment rolls out new Pods automatically) |
| Edit Deployment (image, env, volume, labels, etc.) | ❌ No (automatic rollout)                           |
| Edit a standalone Pod                              | ✅ Recreate the Pod                                 |

---

# 🎯 Your Redis Task

You changed this:

```yaml
configMap:
  name: redis-cofig
```

to

```yaml
configMap:
  name: redis-config
```

Since this modified the **Deployment**, Kubernetes automatically created a new ReplicaSet and a new Pod with the corrected configuration. You did **not** need to manually restart the Pod or any Service.

---

## 💡 Interview Tip

A common interview question is:

> **If I update a ConfigMap, will my application automatically use the new values?**

The best answer is:

* **If the ConfigMap is mounted as a volume:** the mounted files are typically updated automatically.
* **If the ConfigMap is consumed as environment variables:** the running Pod will **not** see the new values. The Pod must be restarted (for example, with `kubectl rollout restart deployment <deployment-name>`) so the environment variables are reloaded.

