# 🚀 Task-03: Deploy a Highly Available NGINX Application Using Deployment & NodePort Service

## 🎯 Objective

The objective of this task is to deploy a **highly available and scalable NGINX web application** using a **Deployment** with multiple replicas and expose it externally using a **NodePort Service**.

This demonstrates how Kubernetes Deployments provide **self-healing**, **scalability**, and **rolling updates**, while a NodePort Service enables external access to the application.

> 💡 **Real-world Scenario:** In production, Deployments manage application Pods, and Services provide stable networking. A NodePort Service is commonly used in development, testing, and on-premises Kubernetes clusters.

---

# 🏗️ Environment Setup

Verify cluster connectivity.

```bash
kubectl get nodes
kubectl get pods
kubectl get deployments
kubectl get svc
```

---

# 📝 Solution

## Method-1: Using YAML (Recommended)

Create **nginx-deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx-container
          image: nginx:latest
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service

spec:
  type: NodePort

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
      nodePort: 30011
```

Apply the manifest.

```bash
kubectl apply -f nginx-deployment.yaml
```

---

## Method-2: Using Imperative Commands

Create Deployment

```bash
kubectl create deployment nginx-deployment --image=nginx:latest
```

Scale to 3 replicas

```bash
kubectl scale deployment nginx-deployment --replicas=3
```

Expose Deployment

```bash
kubectl expose deployment nginx-deployment \
--name=nginx-service \
--type=NodePort \
--port=80 \
--target-port=80
```

Patch NodePort

```bash
kubectl patch svc nginx-service -p '{"spec":{"ports":[{"port":80,"targetPort":80,"nodePort":30011}]}}'
```

---

# ✅ Verification

## Verify Deployment

```bash
kubectl get deployments
```

Expected Output

```text
NAME                READY   UP-TO-DATE   AVAILABLE
nginx-deployment    3/3     3            3
```

---

## Verify ReplicaSets

```bash
kubectl get rs
```

---

## Verify Pods

```bash
kubectl get pods
```

Expected Output

```text
NAME                                READY   STATUS
nginx-deployment-xxxxxxxxxx-abcde   1/1     Running
nginx-deployment-xxxxxxxxxx-fghij   1/1     Running
nginx-deployment-xxxxxxxxxx-klmno   1/1     Running
```

---

## Verify Service

```bash
kubectl get svc
```

Expected Output

```text
NAME              TYPE       CLUSTER-IP      PORT(S)
nginx-service     NodePort   10.xx.xx.xx     80:30011/TCP
```

---

## Verify NodePort

```bash
kubectl describe svc nginx-service
```

---

## Verify Endpoints

```bash
kubectl get endpoints nginx-service
```

---

## Test the Application

```bash
kubectl port-forward svc/nginx-service 8080:80
```

or

```bash
curl http://<Node-IP>:30011
```

---

# 🛠️ Troubleshooting Commands

## Check Deployments

```bash
kubectl get deployments
```

---

## Describe Deployment

```bash
kubectl describe deployment nginx-deployment
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

## Check Service

```bash
kubectl get svc
```

---

## Describe Service

```bash
kubectl describe svc nginx-service
```

---

## Check Endpoints

```bash
kubectl get endpoints
```

---

## Check Labels

```bash
kubectl get pods --show-labels
```

---

## Verify Deployment YAML

```bash
kubectl get deployment nginx-deployment -o yaml
```

---

## Verify Service YAML

```bash
kubectl get svc nginx-service -o yaml
```

---

## Check Rollout Status

```bash
kubectl rollout status deployment/nginx-deployment
```

---

## View Rollout History

```bash
kubectl rollout history deployment/nginx-deployment
```

---

## Scale Deployment

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

---

## Delete Resources

```bash
kubectl delete deployment nginx-deployment
kubectl delete svc nginx-service
```

---

# ❌ Errors Encountered

> **No errors encountered during this task.**
>
> *(This section will be updated only if any issues are encountered while completing the lab.)*

---

# 🧠 Architecture

```text
                    Kubernetes Cluster
                            │
                            │
                    NodePort :30011
                            │
                            ▼
                  nginx-service (NodePort)
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
     Pod-1              Pod-2              Pod-3
 (nginx-container)  (nginx-container)  (nginx-container)
        │                   │                   │
        └───────────────────────────────────────┘
                    Managed by Deployment
                  (nginx-deployment, Replicas=3)
```

---

# 🎤 Interview Questions

### ❓1. What is a Deployment?

A Deployment manages Pods and ReplicaSets, providing scaling, rolling updates, self-healing, and rollback capabilities.

---

### ❓2. Why use a Deployment instead of creating Pods directly?

Because Deployments automatically restart failed Pods, support scaling, rolling updates, and maintain the desired number of replicas.

---

### ❓3. What is a ReplicaSet?

A ReplicaSet ensures that the desired number of Pod replicas are always running.

---

### ❓4. What is a NodePort Service?

A NodePort Service exposes an application externally by opening a port on every Kubernetes node.

---

### ❓5. Difference between ClusterIP and NodePort?

| ClusterIP                 | NodePort                              |
| ------------------------- | ------------------------------------- |
| Internal access only      | External access                       |
| Default service type      | Opens a port on each node             |
| Accessible within cluster | Accessible using `<Node-IP>:NodePort` |

---

### ❓6. How do you scale a Deployment?

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

---

### ❓7. How do you check the rollout status?

```bash
kubectl rollout status deployment/nginx-deployment
```

---

### ❓8. How do you rollback a Deployment?

```bash
kubectl rollout undo deployment/nginx-deployment
```

---

# 🌍 Real-Time DevOps Use Cases

* 🌐 Hosting production web applications.
* ⚖️ Load balancing traffic across multiple Pods.
* 📈 Scaling applications during peak traffic.
* 🔄 Rolling updates with zero downtime.
* 🩺 Self-healing by automatically replacing failed Pods.
* 🚀 Blue-Green and Canary deployment strategies.

---

# 📚 Key Takeaways

* 🚀 Created a **Deployment** with **3 replicas** for high availability.
* 🌐 Exposed the application using a **NodePort Service**.
* ⚖️ Learned how Kubernetes distributes traffic across multiple Pods.
* 🔄 Understood how Deployments provide **self-healing**, **scaling**, and **rolling updates**.
* 🛠️ Practiced essential troubleshooting commands for Deployments, ReplicaSets, Pods, and Services.
* 💼 Gained hands-on experience with one of the most commonly used Kubernetes workloads in production environments.

---
### Question (how to get the node ip and why cluster ip, Nodeport Ips are different, please explain this)

This is one of the **most important Kubernetes networking concepts** and a very common **DevOps interview question**. Let's understand it step by step.

---

# 🌐 Kubernetes Networking Architecture

```text
                          Internet
                              │
                              │
                     http://NodeIP:30011
                              │
                              ▼
                   ┌───────────────────────┐
                   │ Kubernetes Worker Node│
                   │  IP: 192.168.1.100    │  ← Node IP
                   └───────────┬───────────┘
                               │
                     NodePort :30011
                               │
                               ▼
                nginx-service (ClusterIP Service)
                ClusterIP: 10.96.145.21
                               │
              ┌────────────────┼────────────────┐
              ▼                ▼                ▼
         nginx Pod       nginx Pod       nginx Pod
       10.244.0.5      10.244.1.8      10.244.2.6
```

---

# 📌 What is a Node IP?

A **Node IP** is the **IP address of the Kubernetes Worker Node (or VM)** where Pods are running.

Think of it as the IP address of the Linux server.

Example:

```text
Node Name : worker-1
Node IP   : 192.168.1.100
```

---

# 🔍 How to Get the Node IP

### Method 1 (Most Common)

```bash
kubectl get nodes -o wide
```

Example:

```text
NAME       STATUS   ROLES    INTERNAL-IP
worker-1   Ready    <none>   192.168.1.100
```

---

### Method 2

```bash
kubectl describe node worker-1
```

Look for:

```text
Addresses:
InternalIP: 192.168.1.100
```

---

### Method 3 (JSONPath)

```bash
kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}'
```

---

# 📌 What is a Cluster IP?

When you create a Service, Kubernetes automatically assigns an IP like:

```text
10.96.145.21
```

This IP exists **only inside the Kubernetes cluster**.

Example:

```bash
kubectl get svc
```

```text
NAME             TYPE        CLUSTER-IP
nginx-service    NodePort    10.96.145.21
```

You **cannot** access this IP from your laptop.

Only Pods inside the cluster can reach it.

Example:

```
Pod A
   │
   ▼
10.96.145.21
```

---

# 📌 What is a NodePort?

NodePort is **not an IP**.

It is **a port opened on every Kubernetes Node**.

Example:

```
30011
```

When you create

```yaml
type: NodePort
```

Kubernetes opens:

```
Every Node
        │
        ▼
Port 30011
```

Now anyone can access

```
http://NodeIP:30011
```

Example

```
http://192.168.1.100:30011
```

---

# 📌 Why are ClusterIP and Node IP Different?

Because they serve different purposes.

| Node IP                   | Cluster IP                    |
| ------------------------- | ----------------------------- |
| Real Linux Server IP      | Virtual Kubernetes Service IP |
| Exists outside Kubernetes | Exists only inside Kubernetes |
| Assigned by your network  | Assigned by Kubernetes        |
| Used to access NodePort   | Used by Pods inside cluster   |

---

## Example

Node

```
192.168.1.100
```

Service

```
10.96.145.21
```

Pods

```
10.244.0.5
10.244.1.8
10.244.2.6
```

These are **three completely different networks**.

---

# 📌 Why Doesn't the Service Use the Pod IP?

Pods are temporary.

Example:

Today

```
Pod A
10.244.0.5
```

Tomorrow

```
Pod A crashes
```

New Pod

```
10.244.0.18
```

The Pod IP changes.

If users connected directly to Pods, the application would stop working.

Instead,

Users connect to

```
Service
10.96.145.21
```

The Service automatically forwards traffic to whichever Pods are currently running.

---

# 📌 Complete Traffic Flow

```
Browser
     │
     ▼
192.168.1.100:30011
(Node IP + NodePort)
     │
     ▼
nginx-service
ClusterIP
10.96.145.21
     │
     ▼
ReplicaSet
     │
 ┌───┴────────────┐
 ▼                ▼
Pod1            Pod2
```

---

# 📌 Why Do We Need ClusterIP?

Suppose you have:

```
Frontend

Backend

Database
```

The frontend doesn't need to know which backend Pod is running.

It always calls

```
http://backend-service
```

or

```
10.96.x.x
```

Kubernetes automatically sends the request to a healthy backend Pod.

---

# 📌 Interview Question

### ❓Why are Node IP, ClusterIP, and Pod IP different?

**Answer:**

* **Node IP** identifies the Kubernetes node (physical or virtual machine).
* **Pod IP** identifies an individual Pod and changes if the Pod is recreated.
* **ClusterIP** is a stable virtual IP assigned to a Service, allowing clients inside the cluster to reach the application without depending on changing Pod IPs.
* **NodePort** exposes the Service externally by opening a fixed port on every node.

---

# 🎯 Quick Summary

| Component     | Example         | Purpose                            | Accessible From               |
| ------------- | --------------- | ---------------------------------- | ----------------------------- |
| **Node IP**   | `192.168.1.100` | IP of the worker node              | Outside & Inside Cluster      |
| **Pod IP**    | `10.244.1.5`    | IP of an individual Pod            | Inside Cluster                |
| **ClusterIP** | `10.96.145.21`  | Stable Service IP                  | Inside Cluster only           |
| **NodePort**  | `30011`         | External port opened on every node | Outside via `NodeIP:NodePort` |

## 💡 DevOps Tip

In production, you rarely expose applications directly with **NodePort**. A more common architecture is:

```
Internet
    │
    ▼
Load Balancer (AWS ALB / Azure LB / GCP LB)
    │
    ▼
Ingress Controller (NGINX / Traefik)
    │
    ▼
ClusterIP Service
    │
    ▼
Pods
```

This approach provides better scalability, security, and routing capabilities compared to exposing applications directly through NodePort.

---

### Question: (we have created 3 pods using replica set, so only one pod will be running at a time, because i can see only one pod ip)

Great question! This is exactly the kind of thing interviewers ask to test your understanding of Kubernetes networking.

The answer is **No**—**all 3 Pods are running simultaneously**, not one at a time.

---

# 🚀 Deployment with 3 Replicas

When you create:

```yaml
replicas: 3
```

Kubernetes creates:

```text
Deployment
     │
     ▼
ReplicaSet
     │
 ┌───┼───────────┐
 ▼   ▼           ▼
Pod1 Pod2      Pod3
```

All **three Pods** should be in the `Running` state.

Verify with:

```bash
kubectl get pods -o wide
```

Example output:

```text
NAME                                READY   STATUS    IP
nginx-deployment-6d4cf56d5b-abcde   1/1     Running   10.244.0.5
nginx-deployment-6d4cf56d5b-fghij   1/1     Running   10.244.0.6
nginx-deployment-6d4cf56d5b-klmno   1/1     Running   10.244.0.7
```

Notice that each Pod has its **own Pod IP**.

---

# 🤔 Why Do You See Only One IP?

You are probably looking at the **Service**.

For example:

```bash
kubectl get svc
```

Output:

```text
NAME            TYPE       CLUSTER-IP
nginx-service   NodePort   10.96.145.21
```

The Service has **one ClusterIP**:

```text
10.96.145.21
```

This single Service IP represents **all three Pods**.

---

# 📦 How Does One Service Reach Three Pods?

The Service maintains an **Endpoints** list.

Run:

```bash
kubectl get endpoints nginx-service
```

Example:

```text
NAME            ENDPOINTS
nginx-service   10.244.0.5:80,10.244.0.6:80,10.244.0.7:80
```

Now you can see **all three Pod IPs** behind the Service.

---

# 🔄 Traffic Flow

```text
                    Browser
                       │
                       ▼
             NodeIP:30011
                       │
                       ▼
               nginx-service
             ClusterIP:10.96.x.x
                       │
         ┌─────────────┼─────────────┐
         ▼             ▼             ▼
   Pod-1          Pod-2          Pod-3
10.244.0.5    10.244.0.6    10.244.0.7
```

The Service **load balances** requests across all healthy Pods.

---

# 🧪 Verify All Pod IPs

### Check Pods

```bash
kubectl get pods -o wide
```

### Check Endpoints

```bash
kubectl get endpoints nginx-service
```

### Describe the Service

```bash
kubectl describe svc nginx-service
```

You'll see the list of backend endpoints (Pod IPs).

---

# 💡 Interview Question

### ❓If there are 3 replicas, why does the Service have only one IP?

**Answer:**

A Kubernetes Service provides a **single stable virtual IP (ClusterIP)**. Behind that Service are multiple Pod IPs managed through **Endpoints**. The Service automatically distributes incoming requests across the available Pods using kube-proxy (or another service proxy implementation).

---

# 🎯 Quick Summary

| Resource   | Count | IP Address              |
| ---------- | ----: | ----------------------- |
| Deployment |     1 | No IP                   |
| ReplicaSet |     1 | No IP                   |
| Pods       |     3 | **3 different Pod IPs** |
| Service    |     1 | **1 ClusterIP**         |
| Node       |    1+ | **1 Node IP per node**  |

**Remember this rule:**

* 🟢 **One Pod = One Pod IP**
* 🟢 **One Service = One ClusterIP**
* 🟢 **One Node = One Node IP**

The Service hides the multiple Pod IPs behind a single stable address, which is why you typically see only one Service IP even though multiple Pods are serving the application.

---

### Question (let me explain, there will be one cluster and inside the cluster you can have multiple nodes but each node will have only one unique ip where user can access through the browser and the node ip + node port will forward to any one pod to get the information and share back to browser, please check and let me know if i understood correctly.)

You're **very close**—I'd say you've understood about **85–90%** of it. There are just two important clarifications.

---

# ✅ Your Understanding (with minor corrections)

> There will be one cluster.

✔️ Correct.

A Kubernetes cluster is made up of one or more control plane nodes and one or more worker nodes.

```text
Kubernetes Cluster
```

---

> Inside the cluster, you can have multiple nodes.

✔️ Correct.

Example:

```text
                Kubernetes Cluster

        ┌────────────┐
        │ Control    │
        │ Plane      │
        └─────┬──────┘
              │
     ┌────────┴─────────┐
     │                  │
┌────────────┐    ┌────────────┐
│ Worker-1   │    │ Worker-2   │
│192.168.1.10│    │192.168.1.11│
└────────────┘    └────────────┘
```

Each node has its own unique IP address.

---

> Each node will have only one unique IP.

✔️ Correct.

Example:

| Node     | Node IP      |
| -------- | ------------ |
| Worker-1 | 192.168.1.10 |
| Worker-2 | 192.168.1.11 |
| Worker-3 | 192.168.1.12 |

---

## 🔴 This is the only sentence I'd change

You said:

> "the node ip + node port will forward to **any one pod**"

This isn't always true.

A better statement is:

> **The Node IP + NodePort forwards the request to the Service, and the Service load-balances the request to one of the healthy Pods.**

The important distinction is that **NodePort does not directly know about Pods**.

---

# Actual Flow

```text
Browser
     │
     ▼
192.168.1.10:30011
(Node IP + NodePort)
     │
     ▼
NodePort Service
     │
     ▼
ClusterIP Service
     │
     ▼
Load Balancer
(kube-proxy)
     │
 ┌───┼───────────────┐
 ▼   ▼               ▼
Pod1 Pod2          Pod3
```

Notice:

The request first reaches the **Service**, and the Service selects one of the available Pods.

---

# Another Important Point

Suppose:

```
Replica = 3
```

Pods are distributed like this:

```
Worker-1
---------
Pod-1
Pod-2

Worker-2
---------
Pod-3
```

Now imagine the browser accesses:

```
192.168.1.10:30011
```

Even though the request reaches **Worker-1**, Kubernetes may forward it to **Pod-3**, which is running on **Worker-2**.

That's one of the powerful features of Kubernetes networking.

---

# So who decides which Pod receives the request?

Not the browser.

Not the Node.

It is the **Service** (implemented by kube-proxy or another service proxy) that distributes requests across healthy Pods.

---

# Interview Answer (Perfect Version)

If an interviewer asks:

> Explain how NodePort works.

You can answer:

> "A Kubernetes cluster consists of one or more worker nodes, each with its own unique Node IP. A NodePort Service opens the same port on every worker node. When a client accesses `NodeIP:NodePort`, the request first reaches the NodePort Service. The Service then forwards the request to one of the healthy Pods selected by its label selector, distributing traffic across replicas. The selected Pod may be running on the same node or a different node."

That is a strong, production-oriented explanation.

---

# One More Concept You'll Love 🚀

Suppose you have **3 nodes**:

| Node     | IP           |
| -------- | ------------ |
| Worker-1 | 192.168.1.10 |
| Worker-2 | 192.168.1.11 |
| Worker-3 | 192.168.1.12 |

Your Service is:

```
NodePort = 30011
```

You can access the application using **any** of these:

```
192.168.1.10:30011
192.168.1.11:30011
192.168.1.12:30011
```

Even if **all three Pods are running only on Worker-2**, Kubernetes networking will still route the request correctly.

This ability to reach the Service through **any node** while Kubernetes transparently forwards traffic to healthy Pods anywhere in the cluster is one of the key strengths of Kubernetes networking.

---

### Question (Thanks, just want to clarify that in K8s, we have control plane and work plane/work node, in above example i can see there are 2 work node, so how those are created, we have create maually while setuping the K8s or will be auto assinged?)

You're asking exactly the right questions. This is where many beginners get confused, but once you understand it, Kubernetes architecture becomes much clearer.

The short answer is:

> **Worker nodes are not created automatically.** You (or your cloud provider) create them and join them to the Kubernetes cluster.

Let's walk through it.

---

# 🏗️ How a Kubernetes Cluster is Built

Imagine you're setting up Kubernetes from scratch.

Initially, you only have Linux servers (physical machines, VMs, or cloud instances).

For example:

```text
Server-1 (Ubuntu)
Server-2 (Ubuntu)
Server-3 (Ubuntu)
```

At this point, these are just normal Linux machines.

---

## Step 1: Create the Control Plane

You initialize Kubernetes on one server.

```bash
kubeadm init
```

Now:

```text
Server-1
──────────────
Control Plane
API Server
Scheduler
Controller Manager
etcd
```

This becomes the **master/control plane**.

---

## Step 2: Join Worker Nodes

On the remaining servers, you run the join command generated by `kubeadm init`.

Example:

```bash
kubeadm join 192.168.1.100:6443 \
--token abc.xyz \
--discovery-token-ca-cert-hash sha256:xxxxx
```

Now:

```text
                Kubernetes Cluster

        ┌────────────────────────┐
        │    Control Plane       │
        │      Server-1          │
        └───────────┬────────────┘
                    │
         ┌──────────┴──────────┐
         │                     │
┌────────────────┐     ┌────────────────┐
│ Worker Node-1  │     │ Worker Node-2  │
│    Server-2    │     │    Server-3    │
└────────────────┘     └────────────────┘
```

The worker nodes **do not appear automatically**. You explicitly add them to the cluster.

---

# 🌩️ What About AWS, Azure, or GCP?

This is where automation comes in.

Suppose you're using Amazon EKS.

You might say:

> "Create a cluster with **5 worker nodes**."

AWS automatically provisions:

* 1 Control Plane (managed by AWS)
* 5 EC2 instances as Worker Nodes
* Installs Kubernetes components
* Joins them to the cluster

To you, it feels automatic, but behind the scenes, the cloud platform is creating and joining the nodes for you.

---

# 🖥️ What About Minikube?

When you run:

```bash
minikube start
```

You usually get:

```text
1 Control Plane
1 Worker Node
```

on the same virtual machine.

So everything is running inside one VM.

---

# 📦 What About KodeKloud Labs?

Most KodeKloud labs use something like:

```text
Jump Host
     │
     ▼
Kubernetes Cluster
     │
 ┌───┴─────────┐
 │ Worker Node │
```

or sometimes multiple worker nodes.

You don't create them—they're already provisioned for the lab.

---

# 🎯 Real Production Example

Imagine Flipkart or Amazon.

```text
Control Plane
      │
─────────────────────────────────────
Worker-1
Worker-2
Worker-3
Worker-4
Worker-5
Worker-6
Worker-7
Worker-8
Worker-9
Worker-10
```

When traffic increases...

They don't create Pods first.

They create **more Worker Nodes**.

Example:

```text
Old

Worker-1
Worker-2
Worker-3

↓

Auto Scaling

Worker-1
Worker-2
Worker-3
Worker-4
Worker-5
Worker-6
Worker-7
```

Now Kubernetes has more capacity to schedule Pods.

---

# 🤔 Who Decides Which Worker Node Gets a Pod?

This is the job of the **Scheduler**, which runs on the **Control Plane**.

Example:

```text
Deployment
Replicas = 6
```

Scheduler decides:

```text
Worker-1
---------
Pod-1
Pod-2

Worker-2
---------
Pod-3
Pod-4

Worker-3
---------
Pod-5
Pod-6
```

You don't manually assign Pods to nodes in normal scenarios.

---

# 📌 Summary

| Question                                | Answer                                         |
| --------------------------------------- | ---------------------------------------------- |
| Are Worker Nodes created automatically? | ❌ No, not in a self-managed cluster.           |
| Who creates them?                       | 👨‍💻 You (or automation/cloud services).      |
| How do they join the cluster?           | Using `kubeadm join` (or equivalent tooling).  |
| Who decides where Pods run?             | The Kubernetes Scheduler on the Control Plane. |
| Can I add more Worker Nodes later?      | ✅ Yes, anytime to increase cluster capacity.   |

## 💡 One More Important Concept

Think of it like this:

* 🏢 **Control Plane = The manager**. It makes decisions, schedules workloads, and maintains the desired state.
* 🏭 **Worker Nodes = The employees**. They do the actual work by running Pods.

The manager never does the work itself—it assigns work to the employees. That's exactly how Kubernetes is designed.
