🚀 Gig Router DevOps – Kubernetes Manifests

Production-grade Kubernetes manifests for deploying the Gig Router Platform using a GitOps-based workflow.
 
 📋 Table of Contents


- [Overview](#overview)  
- [Architecture](#architecture)
- [Log Flow](#log-flow)  
- [Project Structure](#project-structure)   
- [Argo CD Application](#argo-cd-application)
- [Backend Components](#backend-components)
- [Frontend Components](#frontend-components)  
- [Elasticsearch](#elasticsearch)  
- [Fluent Bit](#fluent-bit)  
- [Kibana](#kibana)  
- [Ingress (AWS ALB)](#ingress-aws-alb)
- [Security](#security)
- [Deployment Workflow](#deployment-workflow)
- [Troubleshooting](#troubleshooting)   


---

### Overview

This repository contains the complete Kubernetes Infrastructure Layer for the Gig Router platform
Deployment strategy:

```text
✅ GitOps-driven delivery via Argo CD
✅ AWS EKS-based Kubernetes platform
✅ Modular application architecture
✅ Production-style workload separation

Core capabilities:

✅ Backend Microservices
✅ Frontend Application
✅ Centralized Logging (EFK Stack)
✅ Traffic Management via AWS ALB
✅ Secure Secret Management
---
```
### Key Features

```text
✅ Backend & Frontend Kubernetes Workloads
✅ Elasticsearch / Fluent Bit / Kibana Logging Stack
✅ AWS ALB Ingress Controller
✅ External Secrets Operator Integration
✅ Cert-Manager TLS Automation
✅ Namespace-Based Isolation
✅ Drift Detection & Self-Healing (Argo CD)
```
### Architecture

#### High-Level Logging Architecture

Users → AWS ALB → Kubernetes Ingress → Services → Pods → Fluent Bit → Elasticsearch → Kibana

![image(1)](https://github.com/user-attachments/assets/036a80c8-7b58-443e-8856-319eb24c58ad)

![Screenshot_11-2-2026_201750_grok com](https://github.com/user-attachments/assets/1cdd9c31-a6e5-4ca4-a4e8-aa64fddb9a1b)


---

### Log Flow

1. Containers write logs → `/var/log/pods`  
2. Fluent Bit (DaemonSet) collects logs  
3. Logs forwarded to Elasticsearch  
4. Kibana queries Elasticsearch
   
<img width="1920" height="863" alt="Screenshot 2026-02-11 201408" src="https://github.com/user-attachments/assets/8a0e8faf-7c3f-491b-8a00-ca1b5ee0c1f0" />

---

### Project Structure

```text
k8s-manifests/
├── argocd/                     # Argo CD Applications (GitOps Control)
│   ├── apps.yaml
│   ├── aws-loadbalancer.yaml
│   ├── backend-app.yaml
│   ├── cert-manager.yaml
│   ├── external-secrets-app.yaml
│   ├── frontend-app.yaml
│   ├── logging-app.yaml
│   ├── memcached-app.yaml
│   ├── monitoring-helm.yaml
│   └── prometheus-crds.yaml
│
├── backend/                    # Backend Application Resources
│   ├── SecretStore.yaml
│   ├── configMap.yaml
│   ├── deployment.yaml
│   ├── ingress.yaml
│   ├── job.yaml
│   ├── redis.yaml
│   └── redis-service.yaml
│
├── frontend/                   # Frontend Application Resources
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   └── networkpolicy.yaml
│
├── logging/                    # EFK Logging Stack
│   ├── elasticsearch.yaml
│   ├── fluentd.yaml
│   ├── kibana.yaml
│   └── ingress.yaml
│
├── memcached/                  # Memcached Helm Values
    └── values.yaml

```
---

### Argo CD Application

**File:** `argocd/`

### Purpose

| Application               | Purpose                    |
| ------------------------- | -------------------------- |
| backend-app.yaml          | Backend services           |
| frontend-app.yaml         | Frontend UI                |
| logging-app.yaml          | EFK logging stack          |
| external-secrets-app.yaml | Secrets integration        |
| cert-manager.yaml         | TLS certificate management |
| memcached-app.yaml        | Caching layer              |
| aws-loadbalancer.yaml     | AWS ALB Controller         | 

---

### Backend Components

**File:** `backend/`

Resources
```text
✅ Deployment
✅ Service
✅ ConfigMap
✅ Redis Cache
✅ Kubernetes Job
✅ Ingress
```
Responsibilities

API Services
Business Logic
Database Connectivity
Caching (Redis)

### Frontend Components

**File:** `frontend/`
Resources
```text
✅ Deployment
✅ Service
✅ Ingress
✅ NetworkPolicy
```
Responsibilities
```text
✅ UI Rendering
✅ Client-side logic
✅ Secure Traffic Routing
```
## Logging Stack

### Elasticsearch

**File:** `logging/elasticsearch.yaml`
### Role
Elasticsearch provides:

```text
✅ Centralized log storage  
✅ Log indexing  
✅ Query engine for Kibana  
```
---

### Key Design Decisions

```text
✅ Single-node deployment
✅ JVM memory optimization
✅ Security disabled (lab / internal mode) 
```
---

### Fluent Bit:
```text
✅ Runs on every node (DaemonSet)
✅ Collects container logs
✅ Forwards logs to Elasticsearch
```
Log Source

path=/var/log/pods/*/*/*.log

Reads logs directly from node filesystem.
Log Destination

output: es
host: elasticsearch
index: fluent-bit

Logs stored in Elasticsearch index:

fluent-bit

### Kibana

File: logging/kibana.yaml
Role

Kibana provides:
```text
✅ Log search
✅ Filtering
✅ Visualization UI
```

### Ingress (AWS ALB)

File: logging/ingress.yaml
Purpose

Expose monitoring tools externally:

✅ Kibana
Example Route

https://kibana.yassinabuelsheikh.store
→ Service: kibana:5601

### Security

Security mechanisms implemented:
```text
✅ External Secrets Operator
✅ AWS Secrets Manager integration
✅ Network Policies
✅ TLS via Cert-Manager
✅ IAM Roles for Service Accounts (IRSA)
```
### Deployment Workflow

GitOps Flow
Developer Push → GitHub → Argo CD → Kubernetes Sync

Deployment Steps

Push manifest changes

Argo CD detects updates

Automatic synchronization

Drift correction (Self-Heal)

### Troubleshooting
Pods Not Updating
kubectl get applications -n argocd
kubectl describe application <app-name>

Logs Missing
kubectl logs -l app=fluent-bit -n monitoring

Ingress Issues
kubectl describe ingress -n monitoring

Secrets Not Injected
kubectl get externalsecret -A
kubectl describe externalsecret <name>

✅ Summary

This repository provides:
```text
✅ Fully GitOps-managed Kubernetes workloads
✅ Modular microservices deployment
✅ Centralized logging & monitoring
✅ Production-style DevOps architecture
```
