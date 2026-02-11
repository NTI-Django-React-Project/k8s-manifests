 📋 Table of Contents


- [Overview](#overview)  
- [Architecture](#architecture)  
- [Log Flow](#log-flow)  
- [Project Structure](#project-structure)  
- [Components](#components)  
- [Argo CD Application](#argo-cd-application)  
- [Elasticsearch](#elasticsearch)  
- [Fluent Bit](#fluent-bit)  
- [Kibana](#kibana)  
- [Ingress (AWS ALB)](#ingress-aws-alb)   


---

## Overview

This setup deploys a **centralized logging stack** inside Kubernetes using:

- **Elasticsearch** → Log storage & indexing  
- **Fluent Bit** → Lightweight log collector  
- **Kibana** → Log visualization & search UI  

The stack is managed via **Argo CD (GitOps workflow)**.

---

## Architecture

### High-Level Logging Architecture

Kubernetes Pods → Fluent Bit → Elasticsearch → Kibana

![Screenshot_11-2-2026_201750_grok com](https://github.com/user-attachments/assets/1cdd9c31-a6e5-4ca4-a4e8-aa64fddb9a1b)


---

## Log Flow

1. Containers write logs → `/var/log/pods`  
2. Fluent Bit (DaemonSet) collects logs  
3. Logs forwarded to Elasticsearch  
4. Kibana queries Elasticsearch
   
<img width="1920" height="863" alt="Screenshot 2026-02-11 201408" src="https://github.com/user-attachments/assets/8a0e8faf-7c3f-491b-8a00-ca1b5ee0c1f0" />

---

## Project Structure

```text
k8s-manifests/
├── argocd/
│   └── logging-app.yaml       # Argo CD Application
│
└── logging/
    ├── elasticsearch.yaml     # Elasticsearch Deployment + Service
    ├── fluentd.yaml           # Fluent Bit DaemonSet
    ├── kibana.yaml            # Kibana Deployment + Service
    └── ingress.yaml           # AWS ALB Ingress

```

---

# 🚀 Argo CD Application

**File:** `argocd/logging-app.yaml`

### Purpose

This Argo CD Application:

✅ Watches Git repository  
✅ Deploys logging stack automatically  
✅ Enables self-healing  
✅ Prunes removed resources  

---

# 🗄 Elasticsearch

**File:** `logging/elasticsearch.yaml`

### Role

Elasticsearch provides:

✅ Centralized log storage  
✅ Log indexing  
✅ Query engine for Kibana  

---

### Key Design Decisions

- Single-node setup  
- Security disabled (lab mode)  
- JVM memory optimization  

---

Fluent Bit:

✅ Runs on every node (DaemonSet)
✅ Collects container logs
✅ Forwards logs to Elasticsearch
Log Source

path=/var/log/pods/*/*/*.log

Reads logs directly from node filesystem.
Log Destination

output: es
host: elasticsearch
index: fluent-bit

Logs stored in Elasticsearch index:

fluent-bit

📊 Kibana

File: logging/kibana.yaml
Role

Kibana provides:

✅ Log search
✅ Filtering
✅ Visualization UI


🌍 Ingress (AWS ALB)

File: logging/ingress.yaml
Purpose

Expose monitoring tools externally:

✅ Grafana
✅ Kibana
✅ Prometheus
Example Route

https://kibana.yassinabuelsheikh.store
→ Service: kibana:5601
