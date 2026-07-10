# 🚀 Task-09: Deploy a Node.js Application on Kubernetes Using Deployment & NodePort Service

## 🎯 Objective

The objective of this task is to deploy a **Node.js application** on a Kubernetes cluster using a **Deployment** with **2 replicas** and expose it externally using a **NodePort Service**.

This task demonstrates how Kubernetes provides **high availability**, **load balancing**, and **self-healing** for web applications.

> 💡 **Real-world Scenario:** Node.js applications are commonly deployed in Kubernetes for REST APIs, microservices, and web applications. A Deployment manages application Pods, while a Service provides stable networking and load balancing.

---

# 🏗️ Environment Setup

Verify the Kubernetes cluster before starting.

```bash
kubectl get nodes
kubectl get deployments
kubectl get svc
kubectl get pods
```

---

# 📝 Solution

Create **node-app-deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-deployment

spec:
  replicas: 2

  selector:
    matchLabels:
      app: node-app

  template:
    metadata:
      labels:
        app: node-app

    spec:
      containers:
        - name: node-container
          image: kodekloud/centos-ssh-enabled:node
          ports:
            - containerPort: 8080

---
apiVersion: v1
kind: Service
metadata:
  name: node-service

spec:
  type: NodePort

  selector:
    app: node-app

  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30012
```

Deploy the resources.

```bash
kubectl apply -f node-app-deployment.yaml
```

---

# ✅ Verification

## Verify Deployment

```bash
kubectl get deployment
```

Expected Output

```text
NAME              READY   UP-TO-DATE   AVAILABLE
node-deployment   2/2     2            2
```

---

## Verify ReplicaSet

```bash
kubectl get rs
```

---

## Verify Pods

```bash
kubectl get pods -o wide
```

Expected Output

```text
NAME                               READY   STATUS
node-deployment-xxxxxxxxxx-abcde   1/1     Running
node-deployment-xxxxxxxxxx-fghij   1/1     Running
```

---

## Verify Service

```bash
kubectl get svc
```

Expected Output

```text
NAME           TYPE       CLUSTER-IP      PORT(S)
node-service   NodePort   10.xx.xx.xx     8080:30012/TCP
```

---

## Verify Endpoints

```bash
kubectl get endpoints
```

Expected Output

```text
NAME           ENDPOINTS
node-service   10.xx.xx.xx:8080,10.xx.xx.xx:8080
```

> ⚠️ **Note:** On Kubernetes v1.33+, you may see:
>
> ```
> Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
> ```
>
> This is only a deprecation warning and does **not** indicate a problem.

---

## Verify Container Image

```bash
kubectl get deployment node-deployment \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

Expected Output

```text
kodekloud/centos-ssh-enabled:node
```

---

## Verify Replicas

```bash
kubectl get deployment node-deployment
```

or

```bash
kubectl describe deployment node-deployment
```

---

## Access the Application

Click the **NodeApp** button provided in the KodeKloud lab.

> 💡 Similar to previous labs, the KodeKloud button uses a **public proxy URL** to access the internal NodePort service.

---

# 🛠️ Troubleshooting Commands

## Check Deployment

```bash
kubectl get deployment
```

---

## Describe Deployment

```bash
kubectl describe deployment node-deployment
```

---

## Check ReplicaSets

```bash
kubectl get rs
```

---

## Describe ReplicaSet

```bash
kubectl describe rs
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

## View Application Logs

```bash
kubectl logs <pod-name>
```

---

## Execute Inside the Container

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

---

## Check Services

```bash
kubectl get svc
```

---

## Describe Service

```bash
kubectl describe svc node-service
```

---

## Check Endpoints

```bash
kubectl get endpoints
```

---

## Check EndpointSlices (Recommended for Kubernetes v1.33+)

```bash
kubectl get endpointslices
```

---

## Verify Labels

```bash
kubectl get pods --show-labels
```

---

## Verify Deployment YAML

```bash
kubectl get deployment node-deployment -o yaml
```

---

## Verify Service YAML

```bash
kubectl get svc node-service -o yaml
```

---

## Check Container Image

```bash
kubectl get deployment node-deployment \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

---

## Validate YAML

```bash
kubectl apply --dry-run=client -f node-app-deployment.yaml
```

---

## Delete Resources

```bash
kubectl delete deployment node-deployment
kubectl delete svc node-service
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
                          │
                          ▼
               Deployment (Replicas = 2)
                  node-deployment
                          │
              ┌───────────┴───────────┐
              ▼                       ▼
       node-container          node-container
  kodekloud/centos-ssh-enabled:node
          Port 8080               Port 8080
              │                       │
              └───────────┬───────────┘
                          ▼
                 NodePort Service
                   node-service
               Port 8080 → 30012
                          │
                          ▼
             KodeKloud Public Proxy
                          │
                          ▼
                     Browser Access
```

---

# 🎤 Interview Questions

### ❓1. Why use a Deployment instead of creating Pods manually?

A Deployment manages Pods automatically, providing self-healing, scaling, rolling updates, and rollback capabilities.

---

### ❓2. Why are there 2 replicas?

Having multiple replicas improves:

* ✅ High Availability
* ✅ Load Balancing
* ✅ Fault Tolerance
* ✅ Zero Downtime during updates

---

### ❓3. How does the Service know which Pods to send traffic to?

The Service uses a **label selector** to identify matching Pods and forwards traffic only to healthy endpoints.

---

### ❓4. What is the difference between `port`, `targetPort`, and `nodePort`?

| Field          | Purpose                                       |
| -------------- | --------------------------------------------- |
| **port**       | Service port inside the cluster               |
| **targetPort** | Container port where the application listens  |
| **nodePort**   | External port opened on every Kubernetes node |

---

### ❓5. How do you verify that both Pods are receiving traffic?

```bash
kubectl get endpoints
```

or (recommended for newer Kubernetes versions):

```bash
kubectl get endpointslices
```

---

### ❓6. How do you scale the Deployment?

```bash
kubectl scale deployment node-deployment --replicas=5
```

---

### ❓7. What happens if one Pod crashes?

The Deployment detects that the desired replica count is no longer met and automatically creates a replacement Pod.

---

### ❓8. Why doesn't KodeKloud use the Node IP directly?

The worker node has only a **private internal IP**, so it is not reachable from your browser. KodeKloud provides a **public proxy URL** that forwards requests to the internal NodePort service.

---

# 🌍 Real-Time DevOps Use Cases

* 🚀 Deploying Node.js REST APIs
* 🌐 Hosting Express.js applications
* 🔄 Microservices architecture
* ⚖️ Load balancing across application replicas
* 📈 Scaling web applications during peak traffic
* ☁️ Running Node.js applications on Kubernetes in production

---

# 📚 Key Takeaways

* 🚀 Deployed a **Node.js application** using a Kubernetes Deployment with **2 replicas**.
* 🌐 Exposed the application using a **NodePort Service** on **30012**.
* ⚖️ Learned how Kubernetes distributes traffic across multiple Pods using label selectors.
* 🔍 Practiced verifying Deployments, ReplicaSets, Pods, Services, Endpoints, and EndpointSlices.
* 🛠️ Understood the relationship between **Deployment → ReplicaSet → Pods → Service**.
* 💼 Gained practical experience deploying and exposing a scalable web application in Kubernetes using production-style concepts.

---

Absolutely! Since you now understand **Node IP, NodePort, ClusterIP, Services, and Pods**, it's better to always visualize the request **from the browser all the way to the container**. This is also how interviewers expect you to explain it.

For **Task-09**, I recommend replacing the Architecture section with the following:

---

# 🧠 Architecture

```text
                       🌐 User Browser
                              │
                              │
                              ▼
      https://NodeApp-button.labs.kodekloud.com
         (KodeKloud Public Proxy URL)
                              │
                              ▼
               Kubernetes Worker Node
                  (Node IP - Internal)
                  10.244.29.243
                              │
                              │
                    NodePort : 30012
                              │
                              ▼
                node-service (NodePort)
                  ClusterIP : 10.43.x.x
                              │
                  Load Balancing (kube-proxy)
                              │
             ┌────────────────┴────────────────┐
             ▼                                 ▼
      Pod-1 (Running)                   Pod-2 (Running)
   node-container                    node-container
Port : 8080                        Port : 8080
Image: kodekloud/centos-ssh-enabled:node
             │                                 │
             └──────────────┬──────────────────┘
                            ▼
                  Response returned
                            │
                            ▼
                   User Browser
```

---

## 🎯 Real Production Architecture

If this were running in AWS, Azure, or GCP instead of KodeKloud, the flow would typically look like this:

```text
                  🌐 User Browser
                         │
                         ▼
               Public IP / DNS Name
                         │
                         ▼
              Load Balancer / Ingress
                         │
                         ▼
              Kubernetes Worker Node
                  NodePort : 30012
                         │
                         ▼
               node-service (ClusterIP)
                         │
                  kube-proxy Routing
                         │
             ┌───────────┴───────────┐
             ▼                       ▼
        Node App Pod-1         Node App Pod-2
           Port 8080              Port 8080
```

---

## 💡 For Your Entire Kubernetes Repository

I recommend using this **standard architecture** for all Deployment + Service tasks by simply replacing the application name, image, port, and NodePort.

```text
                       🌐 User Browser
                              │
                              ▼
        Public URL / Node IP : NodePort
                              │
                              ▼
                 NodePort Service
                              │
                              ▼
                 ClusterIP Service
                              │
                    kube-proxy Routing
                              │
              ┌───────────────┴───────────────┐
              ▼                               ▼
         Application Pod-1              Application Pod-2
              │                               │
              └───────────────┬───────────────┘
                              ▼
                     Response to Browser
```

This diagram accurately reflects the request flow and is suitable for interview explanations. As you progress to **Ingress Controllers**, **Load Balancers**, and **AWS EKS**, you can extend the same architecture rather than replacing it.

---
### QUESTION (curl http:10.2.0.299:8080 worked but curl http:10.2.0.299:30012 did not worked, please explain)

Excellent observation! This actually highlights the difference between a **Pod IP** and a **NodePort**. There's one small detail in your command that makes all the difference.

---

# First, let's identify the IP

You ran:

```bash
curl http://10.2.0.299:8080
```

(I'm assuming you meant a valid Pod IP such as `10.22.0.9` or similar, since `10.2.0.299` isn't a valid IPv4 address.)

This worked.

Then you ran:

```bash
curl http://10.22.0.9:30012
```

This did **not** work.

This is **expected behavior**.

---

# Why did Port 8080 work?

Suppose your Pod looks like this:

```text
Pod IP = 10.22.0.9

Application listens on Port 8080
```

Inside the Pod, Tomcat/Node.js is listening directly on:

```text
10.22.0.9:8080
```

So:

```bash
curl http://10.22.0.9:8080
```

goes **directly to the application**.

```text
curl
   │
   ▼
Pod IP
10.22.0.9
   │
Port 8080
   │
Tomcat / Node Application
```

---

# Why didn't Port 30012 work?

Because **30012 is NOT a Pod port**.

It is a **NodePort**.

NodePort exists only on the **Kubernetes Node**, not on the Pod.

Your Service might look like this:

```yaml
port: 8080
targetPort: 8080
nodePort: 30012
```

This means:

```text
Node
30012
     │
     ▼
Service
     │
     ▼
Pod
8080
```

Notice:

The Pod **never listens on 30012**.

Only the **Node** does.

---

# Correct Request

Suppose:

```text
Node IP = 192.168.1.10

Pod IP = 10.22.0.9

NodePort = 30012
```

Then:

### ✅ Direct Pod Access

```bash
curl http://10.22.0.9:8080
```

works.

---

### ✅ Through Service

```bash
curl http://192.168.1.10:30012
```

works.

---

### ❌ Wrong

```bash
curl http://10.22.0.9:30012
```

fails.

because there is **nothing listening on port 30012 inside the Pod**.

---

# Think of it Like an Apartment 🏢

Imagine:

```text
Apartment Building
```

The building address is:

```text
192.168.1.10
```

Inside:

```text
Room 101
Room 102
Room 103
```

The receptionist is at:

```text
Front Desk
Port 30012
```

Visitors come to:

```text
Building
Front Desk
```

The receptionist sends them to:

```text
Room 101
```

You **cannot** bypass the receptionist by going directly to:

```text
Room101:FrontDesk
```

because Room 101 doesn't have a receptionist.

That's exactly how NodePort works.

---

# Kubernetes Networking

```text
                Browser / curl
                      │
                      ▼
       Node IP :30012  ✅
                      │
               NodePort Service
                      │
               ClusterIP Service
                      │
                kube-proxy
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
Pod-1 :8080                 Pod-2 :8080
```

The Pods expose **8080**, while the Node exposes **30012**.

---

# Verify This Yourself

Run:

```bash
kubectl get svc
```

Example:

```text
NAME           TYPE       PORT(S)

node-service   NodePort   8080:30012/TCP
```

This tells you:

```
8080  ← Service Port
30012 ← Node Port
```

Now check the Pod:

```bash
kubectl describe pod <pod-name>
```

You'll see:

```
Container Port: 8080
```

Not:

```
30012
```

---

# Interview Question

### ❓ Why does `curl PodIP:8080` work, but `curl PodIP:30012` fail?

**Answer:**

`8080` is the application port exposed by the container inside the Pod. The Pod listens directly on that port.

`30012` is a **NodePort** exposed on the Kubernetes node by the Service. The Pod does not listen on `30012`; the Service receives traffic on the node and forwards it to the Pod's `targetPort` (8080).

---

## 💡 One More Tip

To verify what the Service is forwarding to, run:

```bash
kubectl describe svc node-service
```

You'll see something like:

```text
Type:        NodePort
Port:        8080/TCP
TargetPort:  8080/TCP
NodePort:    30012/TCP
Endpoints:   10.22.0.9:8080,10.22.0.10:8080
```

This output clearly shows that the Service accepts traffic on **NodePort 30012** and forwards it to the **Pod endpoints on port 8080**.
