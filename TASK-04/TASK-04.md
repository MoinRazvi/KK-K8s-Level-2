# 🚀 Task-04: Configure Environment Variables in a Kubernetes Pod

## 🎯 Objective

The objective of this task is to create a Kubernetes Pod with **environment variables** and use them within the container command to print a greeting message.

This task demonstrates how to inject configuration into containers using **Environment Variables (`env`)**, which is one of the most common methods for configuring applications in Kubernetes.

> 💡 **Real-world Scenario:** Environment variables are widely used to provide application configuration such as database URLs, API endpoints, application modes, feature flags, and credentials (often through ConfigMaps and Secrets).

---

# 🏗️ Environment Setup

Verify cluster connectivity before starting.

```bash
kubectl get nodes
kubectl get pods
```

---

# 📝 Solution

Create **print-envars-greeting.yaml**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: print-envars-greeting

spec:
  restartPolicy: Never

  containers:
    - name: print-env-container
      image: bash:latest

      env:
        - name: GREETING
          value: "Welcome to"

        - name: COMPANY
          value: "DevOps"

        - name: GROUP
          value: "Industries"

      command:
        - /bin/sh
        - -c
        - echo "$GREETING $COMPANY $GROUP"
```

Create the Pod.

```bash
kubectl apply -f print-envars-greeting.yaml
```

---

# ✅ Verification

## Verify Pod

```bash
kubectl get pods
```

Expected Output

```text
NAME                     READY   STATUS      RESTARTS
print-envars-greeting    0/1     Completed   0
```

---

## View Logs

```bash
kubectl logs -f print-envars-greeting
```

Expected Output

```text
Welcome to DevOps Industries
```

---

## Describe Pod

```bash
kubectl describe pod print-envars-greeting
```

---

## Verify Environment Variables

```bash
kubectl get pod print-envars-greeting -o yaml
```

---

# 🛠️ Troubleshooting Commands

## Check Pod Status

```bash
kubectl get pods
```

---

## Describe Pod

```bash
kubectl describe pod print-envars-greeting
```

---

## View Pod Logs

```bash
kubectl logs print-envars-greeting
```

or

```bash
kubectl logs -f print-envars-greeting
```

---

## Check Environment Variables

```bash
kubectl get pod print-envars-greeting -o yaml
```

Look under:

```yaml
spec:
  containers:
    env:
```

---

## Execute Inside the Pod (if still running)

```bash
kubectl exec -it print-envars-greeting -- /bin/sh
```

Display all environment variables:

```bash
env
```

Display individual variables:

```bash
echo $GREETING
echo $COMPANY
echo $GROUP
```

---

## Delete and Recreate Pod

```bash
kubectl delete pod print-envars-greeting
kubectl apply -f print-envars-greeting.yaml
```

---

## Validate YAML

```bash
kubectl apply --dry-run=client -f print-envars-greeting.yaml
```

---

# ❌ Errors Encountered

> **No errors encountered during this task.**
>
> *(Update this section only if you encounter any issues while completing the lab.)*

---

# 🧠 Architecture

```text
              Kubernetes Pod
      ┌───────────────────────────────┐
      │                               │
      │ print-env-container           │
      │                               │
      │ GREETING = Welcome to         │
      │ COMPANY  = DevOps             │
      │ GROUP    = Industries         │
      │                               │
      │ Command                       │
      │ echo "$GREETING               │
      │      $COMPANY                 │
      │      $GROUP"                  │
      │                               │
      └───────────────┬───────────────┘
                      │
                      ▼
      Welcome to DevOps Industries
```

---

# 🎤 Interview Questions

### ❓1. What are Environment Variables in Kubernetes?

Environment variables allow configuration values to be injected into containers without modifying the application code.

---

### ❓2. Why use Environment Variables?

* Application configuration
* Database connection strings
* API URLs
* Feature flags
* Runtime configuration

---

### ❓3. How do you define Environment Variables?

```yaml
env:
- name: APP_ENV
  value: production
```

---

### ❓4. How do you verify Environment Variables inside a Pod?

```bash
kubectl exec -it <pod-name> -- env
```

or

```bash
kubectl exec -it <pod-name> -- printenv
```

---

### ❓5. What is the difference between Environment Variables and ConfigMaps?

| Environment Variables                | ConfigMap                            |
| ------------------------------------ | ------------------------------------ |
| Stores configuration inside Pod spec | Stores configuration separately      |
| Static after Pod creation            | Can be reused by multiple Pods       |
| Good for simple values               | Better for centralized configuration |

---

### ❓6. Why is `restartPolicy: Never` used?

The container executes the command once and exits successfully. Setting `restartPolicy: Never` prevents Kubernetes from restarting the Pod repeatedly after it completes.

---

### ❓7. How do ConfigMaps and Secrets relate to Environment Variables?

Both ConfigMaps and Secrets can populate environment variables in Pods. ConfigMaps are used for non-sensitive configuration, while Secrets are intended for sensitive data such as passwords, tokens, and API keys.

---

# 🌍 Real-Time DevOps Use Cases

* 🌐 Application environment (`DEV`, `TEST`, `PROD`)
* 🗄️ Database hostnames and ports
* 🔑 API keys (via Secrets)
* 📧 SMTP server configuration
* ☁️ Cloud region and storage bucket names
* ⚙️ Feature flags and runtime options

---

# 📚 Key Takeaways

* 🚀 Created a Pod with custom environment variables.
* 🔧 Learned how to configure containers using the `env` section.
* 📝 Printed environment variable values through the container command.
* 🔄 Understood why `restartPolicy: Never` is appropriate for one-time jobs.
* 🛠️ Practiced inspecting Pods, logs, and environment variables using `kubectl`.
* 💼 Learned one of the most common methods of configuring Kubernetes applications in production.
