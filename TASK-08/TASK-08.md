# � Task-08: Deploy a Tomcat Application on Kubernetes Using Deployment & NodePort Service

## 🎯 Objective

The objective of this task is to deploy a **Java-based Tomcat application** on a Kubernetes cluster using a **Deployment** and expose it externally using a **NodePort Service**.

The application should run inside a dedicated namespace and be accessible through the specified NodePort.

> 💡 **Real-world Scenario:** Java web applications such as Spring Boot, Struts, or traditional WAR-based applications are commonly deployed on Tomcat servers in Kubernetes. Deployments ensure high availability and self-healing, while Services provide stable networking.

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
kubectl create namespace tomcat-namespace-xfusion
```

Verify

```bash
kubectl get ns
```

---

## Step 2️⃣ Create Deployment & Service

Create **tomcat-deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment-xfusion
  namespace: tomcat-namespace-xfusion

spec:
  replicas: 1

  selector:
    matchLabels:
      app: tomcat

  template:
    metadata:
      labels:
        app: tomcat

    spec:
      containers:
        - name: tomcat-container-xfusion
          image: kodekloud/centos-ssh-enabled:tomcat
          ports:
            - containerPort: 8080

---
apiVersion: v1
kind: Service
metadata:
  name: tomcat-service-xfusion
  namespace: tomcat-namespace-xfusion

spec:
  type: NodePort

  selector:
    app: tomcat

  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 32227
```

Apply the manifest.

```bash
kubectl apply -f tomcat-deployment.yaml
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
kubectl get deployment -n tomcat-namespace-xfusion
```

Expected Output

```text
NAME                          READY   UP-TO-DATE   AVAILABLE
tomcat-deployment-xfusion     1/1     1            1
```

---

## Verify Pod

```bash
kubectl get pods -n tomcat-namespace-xfusion -o wide
```

Expected Output

```text
NAME                                           READY   STATUS
tomcat-deployment-xfusion-xxxxxxxxxx-xxxxx     1/1     Running
```

---

## Verify Service

```bash
kubectl get svc -n tomcat-namespace-xfusion
```

Expected Output

```text
NAME                      TYPE       CLUSTER-IP      PORT(S)
tomcat-service-xfusion    NodePort   10.xx.xx.xx     8080:32227/TCP
```

---

## Verify Endpoints

```bash
kubectl get endpoints -n tomcat-namespace-xfusion
```

---

## Verify Container Image

```bash
kubectl get deployment tomcat-deployment-xfusion \
-n tomcat-namespace-xfusion \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

Expected Output

```text
kodekloud/centos-ssh-enabled:tomcat
```

---

## Access the Application

Get the Node IP.

```bash
kubectl get nodes -o wide
```

Open the application in the browser.

```text
http://<Node-IP>:32227
```

The Tomcat welcome page should be displayed.

---

# 🛠️ Troubleshooting Commands

## Check Namespace

```bash
kubectl get ns
```

---

## Check Deployment

```bash
kubectl get deployment -n tomcat-namespace-xfusion
```

---

## Describe Deployment

```bash
kubectl describe deployment tomcat-deployment-xfusion -n tomcat-namespace-xfusion
```

---

## Check ReplicaSets

```bash
kubectl get rs -n tomcat-namespace-xfusion
```

---

## Describe ReplicaSet

```bash
kubectl describe rs -n tomcat-namespace-xfusion
```

---

## Check Pods

```bash
kubectl get pods -n tomcat-namespace-xfusion -o wide
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name> -n tomcat-namespace-xfusion
```

---

## View Application Logs

```bash
kubectl logs <pod-name> -n tomcat-namespace-xfusion
```

---

## Execute Inside the Container

```bash
kubectl exec -it <pod-name> -n tomcat-namespace-xfusion -- /bin/bash
```

---

## Check Services

```bash
kubectl get svc -n tomcat-namespace-xfusion
```

---

## Describe Service

```bash
kubectl describe svc tomcat-service-xfusion -n tomcat-namespace-xfusion
```

---

## Check Endpoints

```bash
kubectl get endpoints -n tomcat-namespace-xfusion
```

---

## Verify Labels

```bash
kubectl get pods -n tomcat-namespace-xfusion --show-labels
```

---

## Verify Deployment YAML

```bash
kubectl get deployment tomcat-deployment-xfusion -n tomcat-namespace-xfusion -o yaml
```

---

## Verify Service YAML

```bash
kubectl get svc tomcat-service-xfusion -n tomcat-namespace-xfusion -o yaml
```

---

## Check Current Namespace

```bash
kubectl config view --minify --output 'jsonpath={..namespace}'
```

---

## Switch to Namespace

```bash
kubectl config set-context --current --namespace=tomcat-namespace-xfusion
```

---

## Show All Resources in Namespace

```bash
kubectl get all -n tomcat-namespace-xfusion
```

---

## Validate YAML

```bash
kubectl apply --dry-run=client -f tomcat-deployment.yaml
```

---

## Delete Resources

```bash
kubectl delete deployment tomcat-deployment-xfusion -n tomcat-namespace-xfusion
kubectl delete svc tomcat-service-xfusion -n tomcat-namespace-xfusion
kubectl delete namespace tomcat-namespace-xfusion
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

             Namespace: tomcat-namespace-xfusion
                          │
                          ▼
             Deployment (Replica = 1)
        tomcat-deployment-xfusion
                          │
                          ▼
          tomcat-container-xfusion
   Image: kodekloud/centos-ssh-enabled:tomcat
                   Port: 8080
                          │
                          ▼
              NodePort Service
          tomcat-service-xfusion
             Port 8080 → 32227
                          │
                          ▼
               Browser Access
         http://<Node-IP>:32227
```

---

# 🎤 Interview Questions

### ❓1. Why deploy Tomcat using a Deployment instead of a Pod?

A Deployment provides self-healing, scaling, rolling updates, and rollback support. If the Tomcat Pod crashes, Kubernetes automatically recreates it.

---

### ❓2. Why use a dedicated namespace?

Namespaces provide logical isolation between applications, making it easier to manage permissions, resources, and environments such as Dev, Test, and Production.

---

### ❓3. What is the purpose of a NodePort Service?

A NodePort Service exposes the application outside the Kubernetes cluster by opening a fixed port on every worker node.

---

### ❓4. How do you verify that Tomcat is running?

```bash
kubectl get deployment -n tomcat-namespace-xfusion
kubectl get pods -n tomcat-namespace-xfusion
kubectl get svc -n tomcat-namespace-xfusion
kubectl logs <pod-name> -n tomcat-namespace-xfusion
```

---

### ❓5. How do you access the Tomcat application?

```text
http://<Node-IP>:32227
```

---

### ❓6. How do you check which image is running?

```bash
kubectl get deployment tomcat-deployment-xfusion \
-n tomcat-namespace-xfusion \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

---

### ❓7. What additional configurations would you use in production?

* Persistent Volumes (PV/PVC)
* Ingress Controller
* HTTPS/TLS
* Liveness Probe
* Readiness Probe
* Resource Requests & Limits
* Horizontal Pod Autoscaler (HPA)

---

### ❓8. Difference between Deployment, Pod, and Service?

| Resource       | Purpose                                                       |
| -------------- | ------------------------------------------------------------- |
| **Pod**        | Runs one or more containers                                   |
| **Deployment** | Manages Pods and ReplicaSets with scaling and rolling updates |
| **Service**    | Provides stable networking and load balancing to Pods         |

---

# 🌍 Real-Time DevOps Use Cases

* ☕ Hosting Java web applications (Spring Boot, WAR files)
* 🚀 Deploying enterprise Java applications
* 🌐 Exposing applications through NodePort, LoadBalancer, or Ingress
* 🔄 Rolling updates with zero downtime
* 📈 Scaling applications using Deployments and HPA
* ☁️ Running Java workloads in Kubernetes clusters

---

# 📚 Key Takeaways

* 🚀 Created a dedicated namespace for the Tomcat application.
* 📦 Deployed Tomcat using a Kubernetes Deployment with one replica.
* 🌐 Exposed the application externally using a NodePort Service on **32227**.
* 🔍 Practiced verifying namespaces, Deployments, ReplicaSets, Pods, Services, Endpoints, and container images.
* 🛠️ Learned how to access and troubleshoot a Java application running inside Kubernetes.
* 💼 Gained practical experience deploying a production-style Java application using Kubernetes best practices.

### QUESTION ASKED (when in enter this address (http://10.244.29.243:32227) it did not worked but worked with kodekloud button give i.e. (https://32227-port-anxfk3hkeecksr4c.labs.kodekloud.com/))

Excellent observation! This is actually an important concept in Kubernetes and cloud/lab environments. Your Kubernetes configuration is correct—the difference is **where you're accessing the application from**.

---

# 🖥️ Your Environment

You have:

```text
Node Name : jump-host
Internal IP : 10.244.29.243
NodePort : 32227
```

So naturally you thought:

```text
http://10.244.29.243:32227
```

should work.

**Normally, in a real Kubernetes cluster, that is correct.**

---

# 🚫 Why Doesn't It Work in KodeKloud?

Look at your node information:

```text
EXTERNAL-IP   <none>
```

This means:

* ❌ The Kubernetes node does **not** have a public IP.
* ❌ The IP `10.244.29.243` is a **private/internal IP** inside KodeKloud's lab network.
* ❌ Your browser on your local machine cannot directly reach that private IP.

Think of it like this:

```text
Your Laptop
      │
      │  Internet
      ▼
-----------------------------
KodeKloud Infrastructure
      │
      ▼
Jump Host
10.244.29.243
```

Your laptop has **no route** to the private `10.x.x.x` network inside the lab.

---

# 🌐 How Does the KodeKloud Button Work?

When you click:

```text
https://32227-port-anxfk3hkeecksr4c.labs.kodekloud.com/
```

KodeKloud creates a **public proxy** that forwards traffic to your NodePort.

The flow is:

```text
Browser
     │
     ▼
https://32227-port-anxfk3hkeecksr4c.labs.kodekloud.com
     │
     ▼
KodeKloud Proxy
     │
     ▼
10.244.29.243:32227
     │
     ▼
NodePort Service
     │
     ▼
Tomcat Pod
```

So, the KodeKloud URL acts as a bridge between the public internet and your private Kubernetes node.

---

# 🌍 What Happens in a Real Production Cluster?

Imagine an AWS cluster.

```
Worker Node
Public IP: 52.12.10.20

NodePort: 32227
```

You could directly access:

```text
http://52.12.10.20:32227
```

because the node has a **publicly reachable IP address**.

---

# ☁️ What About Private Production Clusters?

In many organizations, worker nodes **also don't have public IPs**.

Instead, traffic flows like this:

```text
Internet
    │
    ▼
AWS Load Balancer / Azure LB
    │
    ▼
Ingress Controller
    │
    ▼
ClusterIP Service
    │
    ▼
Pods
```

In these environments, users never access `NodeIP:NodePort` directly.

---

# 🔍 How Can You Verify the Service Is Working?

Even without the KodeKloud proxy, you can test it **from inside the cluster**.

### Option 1: Port Forward

```bash
kubectl port-forward svc/tomcat-service-xfusion 8080:8080 -n tomcat-namespace-xfusion
```

Then open:

```text
http://localhost:8080
```

---

### Option 2: Test from the Node

SSH into the node (or use the jump host if it's the node) and run:

```bash
curl http://localhost:32227
```

or

```bash
curl http://10.244.29.243:32227
```

If networking is configured appropriately, you'll receive the Tomcat page.

---

# 💡 Interview Question

### ❓ Why couldn't you access `http://10.244.29.243:32227`, but the KodeKloud URL worked?

**Answer:**

The IP `10.244.29.243` is a **private internal IP** assigned to the Kubernetes node within the KodeKloud lab environment. It is not reachable from the public internet. KodeKloud provides a public proxy URL that forwards requests from the browser to the internal NodePort service, allowing access to the application without exposing the node directly.

---

# 🎯 Quick Comparison

| Environment                    | Access Method                                      |
| ------------------------------ | -------------------------------------------------- |
| **KodeKloud Lab**              | Public proxy URL (`https://...labs.kodekloud.com`) |
| **Minikube (Local)**           | `http://$(minikube ip):NodePort`                   |
| **AWS EC2 with Public IP**     | `http://Public-IP:NodePort`                        |
| **Private Production Cluster** | Load Balancer / Ingress (not direct NodePort)      |

This distinction between **internal node networking** and **external access through a proxy or load balancer** is a key concept in Kubernetes networking and often comes up in DevOps interviews.
