
# Kubernetes GitOps Infrastructure Portfolio

<p align="center">
  <strong>A Self-Hosted Kubernetes Platform Managed with Argo CD, GitOps, Helm, and Observability Tools</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Kubernetes-Platform-326CE5?logo=kubernetes&logoColor=white" />
  <img src="https://img.shields.io/badge/GitOps-Argo%20CD-EF7B4D?logo=argo&logoColor=white" />
  <img src="https://img.shields.io/badge/Monitoring-Prometheus-E6522C?logo=prometheus&logoColor=white" />
  <img src="https://img.shields.io/badge/Dashboards-Grafana-F46800?logo=grafana&logoColor=white" />
  <img src="https://img.shields.io/badge/Logging-VictoriaLogs-5B6ABF?logo=victoriametrics&logoColor=white" />
  <img src="https://img.shields.io/badge/Networking-MetalLB-00AEEF?logo=kubernetes&logoColor=white" />
</p>

---

## 1. Project Overview

This repository contains the declarative configuration for a self-hosted Kubernetes infrastructure platform.

The primary objective is to demonstrate practical skills in:

- Kubernetes cluster administration and workload management
- GitOps-based infrastructure management with Argo CD
- Declarative application deployment using Kubernetes manifests
- Helm-based installation and lifecycle management
- Container networking and external service exposure
- Infrastructure monitoring and metrics collection
- Centralized log collection and storage
- Persistent storage for stateful applications
- Secure external connectivity using Cloudflare Tunnel

The platform runs a collection of infrastructure services and applications on a multi-node Kubernetes cluster.

The Git repository acts as the source of truth for the resources managed by Argo CD.

### Current platform components

| Component | Purpose |
|---|---|
| Kubernetes | Container orchestration platform |
| Argo CD | GitOps continuous delivery |
| Helm | Packaging and deployment of platform components |
| WordPress | Self-hosted web application |
| MySQL 8.0 | WordPress database |
| MetalLB | LoadBalancer implementation for bare-metal Kubernetes |
| Prometheus | Metrics collection and monitoring |
| Grafana | Metrics visualization and dashboards |
| kube-state-metrics | Kubernetes object state metrics |
| Node Exporter | Host-level metrics |
| Alertmanager | Alert handling |
| VictoriaLogs | Centralized log storage |
| VictoriaLogs Collector | Log collection and forwarding |
| Cloudflare Tunnel | Secure external connectivity |

---

## 2. Architecture

### 2.1 High-Level Infrastructure Architecture

The following diagram illustrates the major components and their relationships.

```mermaid
flowchart TB
    USER["Users / Administrators"]
    GITHUB["GitHub Repository<br/>Source of Truth"]
    ARGO["Argo CD<br/>GitOps Controller"]

    subgraph K8S["Kubernetes Cluster"]
        subgraph APP["Application Layer"]
            WP["WordPress"]
            DB["MySQL 8.0"]
            WPVOL[("Persistent Volumes")]
            WP --> DB
            WP --> WPVOL
            DB --> WPVOL
        end

        subgraph OBS["Observability Layer"]
            PROM["Prometheus"]
            GRAF["Grafana"]
            ALERT["Alertmanager"]
            KSM["kube-state-metrics"]
            NODE["Node Exporter"]
            VLOG["VictoriaLogs Server"]
            VLC["VictoriaLogs Collector"]

            PROM --> GRAF
            PROM --> ALERT
            KSM --> PROM
            NODE --> PROM
            VLC --> VLOG
        end

        subgraph NET["Networking Layer"]
            METALLB["MetalLB"]
            LB["LoadBalancer Services"]
            TUNNEL["Cloudflare Tunnel"]
            METALLB --> LB
        end
    end

    GITHUB -->|"Git repository sync"| ARGO
    ARGO -->|"Reconciliation"| K8S

    USER --> WP
    USER --> GRAF
    LB --> WP
    LB --> GRAF
    TUNNEL --> WP

    style K8S fill:#f5f7fa,stroke:#326CE5,stroke-width:2px
    style GITHUB fill:#24292e,color:#fff
    style ARGO fill:#ef7b4d,color:#fff
    style VLOG fill:#5b6abf,color:#fff


