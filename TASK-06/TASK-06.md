# 🚀 Task-06: Deploy Jenkins on Kubernetes Using Deployment & NodePort Service

## 🎯 Objective

The objective of this task is to deploy a **Jenkins CI Server** on a Kubernetes cluster using a **Deployment** and expose it externally using a **NodePort Service**.

The Jenkins setup should skip the initial setup wizard by configuring the required environment variable, making it ready for automated CI/CD pipeline creation.

> 💡 **Real-world Scenario:** Jenkins is one of the most widely used CI/CD tools. In production, Jenkins is commonly deployed on Kubernetes with persistent storage, external authentication, and ingress controllers for secure access.

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
kubectl create namespace jenkins
```

Verify

```bash
kubectl get ns
```

---

## Step 2️⃣ Create Deployment & Service

Create **jenkins-deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins-deployment
  namespace: jenkins
  labels:
    app: jenkins

spec:
  replicas: 1

  selector:
    matchLabels:
      app: jenkins

  template:
    metadata:
      labels:
        app: jenkins

    spec:
      containers:
        - name: jenkins-container
          image: jenkins/jenkins
          ports:
            - containerPort: 8080

          env:
            - name: JAVA_OPTS
              value: "-Djenkins.install.runSetupWizard=false"

---
apiVersion: v1
kind: Service
metadata:
  name: jenkins-service
  namespace: jenkins

spec:
  type: NodePort

  selector:
    app: jenkins

  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30008
```

Apply the manifest.

```bash
kubectl apply -f jenkins-deployment.yaml
```

---

# ✅ Verification

## Verify Namespace

```bash
kubectl get ns
```

---

## Verify Deployment

```bash
kubectl get deployment -n jenkins
```

Expected Output

```text
NAME                 READY   UP-TO-DATE   AVAILABLE
jenkins-deployment   1/1     1            1
```

---

## Verify Pod

```bash
kubectl get pods -n jenkins -o wide
```

Expected Output

```text
NAME                                  READY   STATUS
jenkins-deployment-xxxxxxxxxx-xxxxx   1/1     Running
```

---

## Verify Service

```bash
kubectl get svc -n jenkins
```

Expected Output

```text
NAME              TYPE       CLUSTER-IP      PORT(S)
jenkins-service   NodePort   10.xx.xx.xx     8080:30008/TCP
```

---

## Verify Endpoints

```bash
kubectl get endpoints -n jenkins
```

---

## Verify Environment Variable

```bash
kubectl describe deployment jenkins-deployment -n jenkins
```

or

```bash
kubectl get deployment jenkins-deployment -n jenkins -o yaml
```

---

## Access Jenkins UI

Get Node IP

```bash
kubectl get nodes -o wide
```

Open in browser

```text
http://<Node-IP>:30008
```

You should see the Jenkins home page without the initial setup wizard.

---

# 🛠️ Troubleshooting Commands

## Check Namespace

```bash
kubectl get ns
```

---

## Check Deployment

```bash
kubectl get deployment -n jenkins
```

---

## Describe Deployment

```bash
kubectl describe deployment jenkins-deployment -n jenkins
```

---

## Check ReplicaSets

```bash
kubectl get rs -n jenkins
```

---

## Describe ReplicaSet

```bash
kubectl describe rs -n jenkins
```

---

## Check Pods

```bash
kubectl get pods -n jenkins -o wide
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name> -n jenkins
```

---

## View Jenkins Logs

```bash
kubectl logs <pod-name> -n jenkins
```

---

## Execute Inside Jenkins Container

```bash
kubectl exec -it <pod-name> -n jenkins -- /bin/bash
```

---

## Check Services

```bash
kubectl get svc -n jenkins
```

---

## Describe Service

```bash
kubectl describe svc jenkins-service -n jenkins
```

---

## Check Endpoints

```bash
kubectl get endpoints -n jenkins
```

---

## Verify Labels

```bash
kubectl get pods -n jenkins --show-labels
```

---

## Verify Deployment YAML

```bash
kubectl get deployment jenkins-deployment -n jenkins -o yaml
```

---

## Verify Service YAML

```bash
kubectl get svc jenkins-service -n jenkins -o yaml
```

---

## Check Container Image

```bash
kubectl get deployment jenkins-deployment \
-n jenkins \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

---

## Check Environment Variables

```bash
kubectl get deployment jenkins-deployment \
-n jenkins \
-o=jsonpath='{.spec.template.spec.containers[*].env}'
```

---

## Validate YAML

```bash
kubectl apply --dry-run=client -f jenkins-deployment.yaml
```

---

## Delete Resources

```bash
kubectl delete deployment jenkins-deployment -n jenkins
kubectl delete svc jenkins-service -n jenkins
kubectl delete namespace jenkins
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

                  Namespace : jenkins
                         │
                         ▼
                Deployment (1 Replica)
               jenkins-deployment
                         │
                         ▼
              jenkins-container
             Image: jenkins/jenkins
               Port : 8080
                         │
                         ▼
               NodePort Service
               jenkins-service
             Port 8080 → 30008
                         │
                         ▼
                Browser Access
          http://<Node-IP>:30008
```

---

# 🎤 Interview Questions

### ❓1. Why is Jenkins deployed using a Deployment instead of a Pod?

A Deployment provides self-healing, scaling, rolling updates, and rollback capabilities, making it suitable for managing production workloads.

---

### ❓2. Why is a NodePort Service used?

A NodePort Service exposes Jenkins externally so it can be accessed from a browser using the Node IP and the assigned NodePort.

---

### ❓3. What is the purpose of the `JAVA_OPTS` environment variable?

The `JAVA_OPTS` environment variable passes JVM options to Jenkins. In this task, it disables the initial setup wizard:

```text
-Djenkins.install.runSetupWizard=false
```

This allows Jenkins to start without requiring manual setup.

---

### ❓4. How do you verify that the environment variable is configured?

```bash
kubectl describe deployment jenkins-deployment -n jenkins
```

or

```bash
kubectl get deployment jenkins-deployment -n jenkins -o yaml
```

---

### ❓5. How do you access Jenkins running in Kubernetes?

```text
http://<Node-IP>:30008
```

---

### ❓6. What would you add in a production Jenkins deployment?

* Persistent Volumes (PV/PVC)
* Ingress Controller
* TLS/HTTPS
* Authentication (LDAP/OAuth/SAML)
* Backup strategy
* Resource requests and limits
* Liveness and Readiness probes

---

### ❓7. Why shouldn't Jenkins data be stored only inside the container?

Containers are ephemeral. If the Pod is recreated, all Jenkins jobs, plugins, and configuration would be lost unless persistent storage is used.

---

# 🌍 Real-Time DevOps Use Cases

* 🚀 CI/CD pipeline automation
* 🔄 Automated application builds and deployments
* 🧪 Test automation
* 📦 Docker image builds
* ☸️ Kubernetes application deployments
* ☁️ Cloud infrastructure automation

---

# 📚 Key Takeaways

* 🚀 Created a dedicated **Jenkins namespace**.
* 📦 Deployed Jenkins using a **Deployment** with one replica.
* 🌐 Exposed Jenkins externally using a **NodePort Service**.
* ⚙️ Configured the `JAVA_OPTS` environment variable to bypass the initial setup wizard.
* 🔍 Practiced verifying Deployments, Pods, Services, Endpoints, and environment variables using `kubectl`.
* 💼 Learned the foundational Kubernetes architecture for running Jenkins, which can later be enhanced with persistent storage, ingress, and production-grade security.

### Question (How can i check whether i am which namespace, can i enter into jenkins namespace.)
---

Excellent question! This is a very common confusion when starting with Kubernetes.

The answer is:

> **You cannot "enter" a namespace like you enter a Linux directory.** A namespace is a **logical grouping of Kubernetes resources**, not a separate environment.

However, you can **set your current/default namespace** so you don't have to specify `-n jenkins` with every command.

---

# 1️⃣ Check Your Current Namespace

Run:

```bash
kubectl config view --minify --output 'jsonpath={..namespace}'
```

If nothing is returned, it means you're using the **default** namespace.

Example:

```text
default
```

or

```text
```

(blank = default namespace)

---

# 2️⃣ List All Namespaces

```bash
kubectl get namespaces
```

Output:

```text
NAME              STATUS   AGE
default           Active   5d
kube-system       Active   5d
kube-public       Active   5d
jenkins           Active   10m
nautilus          Active   30m
```

---

# 3️⃣ View Resources in a Namespace

Pods in Jenkins namespace:

```bash
kubectl get pods -n jenkins
```

Deployments:

```bash
kubectl get deployments -n jenkins
```

Services:

```bash
kubectl get svc -n jenkins
```

---

# 4️⃣ Switch Your Default Namespace ✅

Instead of typing:

```bash
kubectl get pods -n jenkins
```

every time, you can set the default namespace:

```bash
kubectl config set-context --current --namespace=jenkins
```

Now simply run:

```bash
kubectl get pods
```

Output:

```text
NAME                                  READY   STATUS
jenkins-deployment-xxxxx              1/1     Running
```

Notice you no longer need `-n jenkins`.

---

# 5️⃣ Verify the Current Namespace

```bash
kubectl config view --minify | grep namespace
```

Output:

```text
namespace: jenkins
```

---

# 6️⃣ Switch Back to Default

```bash
kubectl config set-context --current --namespace=default
```

Verify:

```bash
kubectl config view --minify | grep namespace
```

Output:

```text
namespace: default
```

---

# 7️⃣ See Resources Across All Namespaces

```bash
kubectl get pods --all-namespaces
```

or

```bash
kubectl get pods -A
```

Example:

```text
NAMESPACE      NAME
default        nginx-pod
jenkins        jenkins-deployment-xxxxx
nautilus       httpd-deploy-xxxxx
kube-system    coredns-xxxxx
```

---

# 8️⃣ Execute Commands Inside a Pod in the Jenkins Namespace

If your Jenkins Pod is named:

```text
jenkins-deployment-7d6b8d6c8f-abcde
```

You can enter it with:

```bash
kubectl exec -it jenkins-deployment-7d6b8d6c8f-abcde -n jenkins -- /bin/bash
```

or, after switching your default namespace to `jenkins`:

```bash
kubectl exec -it jenkins-deployment-7d6b8d6c8f-abcde -- /bin/bash
```

---

# 🎯 Interview Tip

An interviewer may ask:

> **Can we "log in" to a Kubernetes namespace?**

The correct answer is:

> **No.** A namespace is a logical isolation mechanism for Kubernetes resources. You cannot enter it like a Linux directory or shell. Instead, you target a namespace using the `-n` flag or set it as the default namespace in your current kubectl context.

---

## 📌 Most Useful Namespace Commands

| Command                                                          | Purpose                                              |
| ---------------------------------------------------------------- | ---------------------------------------------------- |
| `kubectl get ns`                                                 | List all namespaces                                  |
| `kubectl get pods -n jenkins`                                    | List Pods in the `jenkins` namespace                 |
| `kubectl get all -n jenkins`                                     | List all resources in the `jenkins` namespace        |
| `kubectl get pods -A`                                            | List Pods across all namespaces                      |
| `kubectl config set-context --current --namespace=jenkins`       | Set the default namespace to `jenkins`               |
| `kubectl config view --minify --output 'jsonpath={..namespace}'` | Show the current default namespace                   |
| `kubectl exec -it <pod-name> -n jenkins -- /bin/bash`            | Open a shell inside a Pod in the `jenkins` namespace |
