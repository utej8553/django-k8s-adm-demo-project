# 🚀 Django K8s ADM Demo Project

A simple **Django Todo Application** deployed on Kubernetes using **kubeadm (ADM cluster)** on AWS EC2.

---

## 📌 Overview

This project demonstrates:

- ✅ Namespace creation  
- ✅ Pod deployment  
- ✅ Deployment with replicas  
- ✅ Service exposure (LoadBalancer → NodePort fallback)  
- ✅ Ingress configuration  
- ✅ AWS EC2 networking considerations  

---

## 🏗 Project Architecture


User
↓
EC2 Public IP : NodePort
↓
Kubernetes Service
↓
Deployment (ReplicaSet)
↓
Pods
↓
Django Container


---

## 📦 Kubernetes Objects Used

- **Namespace**
- **Pod**
- **Deployment** (2 replicas)
- **Service** (LoadBalancer type)
- **Ingress**

---

## 📂 Kubernetes Manifests

### 1️⃣ Namespace

All resources are created inside:


django-todo-app


---

### 2️⃣ Deployment

- **Name:** `django-app-deployment`
- **Replicas:** 2
- **Image:** `trainwithshubham/django-todo:latest`
- **Container Port:** 8000

Creates two running Django pods with label:


app: django-app


---

### 3️⃣ Standalone Pod

- **Name:** `django-todo-pod`
- **Image:** `trainwithshubham/django-todo:latest`
- **Container Port:** 8001

> ⚠️ This pod is independent and not managed by the Deployment.

---

### 4️⃣ Service

- **Name:** `django-todo-service`
- **Type:** LoadBalancer
- **Exposes Port:** 80
- **Target Port:** 8000

In a **kubeadm setup**, `LoadBalancer` falls back to:


NodePort (example: 31388)


Access format:


http://<EC2_PUBLIC_IP>:<NodePort>


---

### 5️⃣ Ingress

- **Host:** `django-todo-example-batch-2.info`
- Routes traffic to `django-todo-service` on port 80.

> ⚠️ Requires an Ingress Controller to be installed in the cluster.

---

## 🚀 Deployment Steps

### 1️⃣ Apply Namespace

```bash
kubectl apply -f namespace.yaml
2️⃣ Apply Deployment
kubectl apply -f deployment.yaml
3️⃣ Apply Standalone Pod (Optional)
kubectl apply -f pod.yaml
4️⃣ Apply Service
kubectl apply -f service.yaml
5️⃣ Apply Ingress
kubectl apply -f ingress.yaml
🔍 Verify Resources
kubectl get pods -n django-todo-app
kubectl get svc -n django-todo-app
kubectl get endpoints -n django-todo-app
🌍 Accessing the Application

Since this is a kubeadm cluster (not EKS):

EXTERNAL-IP will show <pending>

The Service exposes a NodePort automatically

Find the NodePort:

kubectl get svc -n django-todo-app

Access the app:

http://<EC2_PUBLIC_IP>:<NodePort>
⚠️ AWS EC2 Security Group Requirement

You must open the NodePort in the EC2 Security Group:

Type: Custom TCP
Port: <NodePort>
Source: 0.0.0.0/0

Without this rule, the application will not be accessible.
