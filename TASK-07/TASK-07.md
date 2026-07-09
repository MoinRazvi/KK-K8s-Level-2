# 🚀 Task-07: Deploy Grafana on Kubernetes Using Deployment & NodePort Service

## 🎯 Objective

The objective of this task is to deploy **Grafana** on a Kubernetes cluster using a **Deployment** and expose it externally using a **NodePort Service**.

Grafana is an open-source visualization and monitoring platform used to create dashboards and analyze metrics collected from monitoring tools such as **Prometheus**, **Datadog**, **InfluxDB**, **CloudWatch**, and many others.

> 💡 **Real-world Scenario:** In production, Grafana is deployed to visualize infrastructure metrics, Kubernetes cluster health, application performance, logs, and business dashboards.

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

Create **grafana-deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-xfusion

spec:
  replicas: 1

  selector:
    matchLabels:
      app: grafana

  template:
    metadata:
      labels:
        app: grafana

    spec:
      containers:
        - name: grafana-container
          image: grafana/grafana:latest
          ports:
            - containerPort: 3000

---
apiVersion: v1
kind: Service
metadata:
  name: grafana-service

spec:
  type: NodePort

  selector:
    app: grafana

  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 32000
```

Deploy the resources.

```bash
kubectl apply -f grafana-deployment.yaml
```

---

# ✅ Verification

## Verify Deployment

```bash
kubectl get deployments
```

Expected Output

```text
NAME                         READY   UP-TO-DATE   AVAILABLE
grafana-deployment-xfusion   1/1     1            1
```

---

## Verify Pod

```bash
kubectl get pods -o wide
```

Expected Output

```text
NAME                                         READY   STATUS
grafana-deployment-xfusion-xxxxxxxxxx-xxxxx  1/1     Running
```

---

## Verify Service

```bash
kubectl get svc
```

Expected Output

```text
NAME              TYPE       CLUSTER-IP      PORT(S)
grafana-service   NodePort   10.xx.xx.xx     3000:32000/TCP
```

---

## Verify Endpoints

```bash
kubectl get endpoints
```

---

## Access Grafana

Get the Node IP.

```bash
kubectl get nodes -o wide
```

Open the browser.

```text
http://<Node-IP>:32000
```

You should see the **Grafana Login Page**.

---

# 🛠️ Troubleshooting Commands

## Check Deployment

```bash
kubectl get deployment
```

---

## Describe Deployment

```bash
kubectl describe deployment grafana-deployment-xfusion
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

## View Grafana Logs

```bash
kubectl logs <pod-name>
```

---

## Execute Inside Grafana Container

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
kubectl describe svc grafana-service
```

---

## Check Endpoints

```bash
kubectl get endpoints
```

---

## Verify Labels

```bash
kubectl get pods --show-labels
```

---

## Verify Deployment YAML

```bash
kubectl get deployment grafana-deployment-xfusion -o yaml
```

---

## Verify Service YAML

```bash
kubectl get svc grafana-service -o yaml
```

---

## Check Container Image

```bash
kubectl get deployment grafana-deployment-xfusion \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

---

## Verify NodePort

```bash
kubectl get svc grafana-service
```

or

```bash
kubectl describe svc grafana-service
```

---

## Validate YAML

```bash
kubectl apply --dry-run=client -f grafana-deployment.yaml
```

---

## Delete Resources

```bash
kubectl delete deployment grafana-deployment-xfusion
kubectl delete svc grafana-service
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
              grafana-deployment-xfusion
                     (Replica = 1)
                           │
                           ▼
                  grafana-container
              Image: grafana/grafana
                     Port : 3000
                           │
                           ▼
                 NodePort Service
                 grafana-service
               Port 3000 → 32000
                           │
                           ▼
                    Browser Access
              http://<Node-IP>:32000
```

---

# 🎤 Interview Questions

### ❓1. What is Grafana?

Grafana is an open-source visualization platform used to create dashboards and monitor infrastructure, applications, Kubernetes clusters, databases, and cloud services.

---

### ❓2. Why is Grafana commonly used with Prometheus?

Prometheus collects and stores metrics, while Grafana queries those metrics and displays them in interactive dashboards.

**Prometheus = Data Collection**
**Grafana = Data Visualization**

---

### ❓3. What port does Grafana use by default?

```text
3000
```

---

### ❓4. Why is a NodePort Service used?

A NodePort Service exposes Grafana outside the Kubernetes cluster so users can access the web interface through:

```text
http://<Node-IP>:32000
```

---

### ❓5. How do you verify that Grafana is running?

```bash
kubectl get deployment
kubectl get pods
kubectl get svc
kubectl logs <pod-name>
```

---

### ❓6. How do you check which image is deployed?

```bash
kubectl get deployment grafana-deployment-xfusion \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

---

### ❓7. What monitoring stack is commonly deployed with Grafana?

A typical Kubernetes monitoring stack includes:

* 📊 Grafana
* 📈 Prometheus
* 🔍 Alertmanager
* 📦 Node Exporter
* ☸️ kube-state-metrics

---

### ❓8. What additional components would you configure for Grafana in production?

* Persistent Volume (PV/PVC)
* Ingress Controller
* TLS/HTTPS
* Authentication (LDAP/OAuth/SAML)
* Role-Based Access Control (RBAC)
* Backup strategy
* Resource requests and limits

---

# 🌍 Real-Time DevOps Use Cases

* 📊 Infrastructure Monitoring
* ☸️ Kubernetes Cluster Monitoring
* 🚀 Application Performance Monitoring (APM)
* 📈 Business Dashboards
* 📜 Log Analytics (Loki)
* ☁️ Cloud Monitoring (AWS, Azure, GCP)
* 🔔 Alert Visualization

---

# 📚 Key Takeaways

* 🚀 Deployed **Grafana** using a Kubernetes Deployment.
* 🌐 Exposed Grafana externally using a **NodePort Service** on **32000**.
* 📊 Learned Grafana's role as a visualization tool in modern monitoring stacks.
* 🔍 Practiced verifying Deployments, Pods, Services, Endpoints, and container images.
* 🛠️ Gained hands-on experience deploying a production-grade monitoring application on Kubernetes.
* 💼 Understood how Grafana integrates with monitoring tools such as **Prometheus**, **Loki**, and **CloudWatch** to provide centralized observability.
