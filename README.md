# 🚀 Enterprise-Grade GitOps Infrastructure Portfolio

![Ansible](https://img.shields.io/badge/Ansible-EE0000?style=for-the-badge&logo=ansible&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Argo CD](https://img.shields.io/badge/Argo_CD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)
![Helm](https://img.shields.io/badge/Helm-0F1689?style=for-the-badge&logo=helm&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white)
![Cloudflare](https://img.shields.io/badge/Cloudflare-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)

Welcome to my comprehensive infrastructure-as-code (IaC) and GitOps portfolio. 

This repository is a production-ready blueprint showcasing a fully automated, self-hosted, multi-node Kubernetes ecosystem. It demonstrates end-to-end infrastructure lifecycle management—from bare-metal provisioning using **Ansible** to declarative continuous delivery via **Argo CD**, complete with advanced observability and centralized logging.

This Git repository serves as the absolute Single Source of Truth (SSoT) for the entire cluster state.

---

## 🌟 1. Project Vision & Key Achievements

The primary goal of this project is to build an enterprise-level, resilient, and highly observable Kubernetes platform on a local network, fully isolated from public cloud dependencies, yet securely accessible from the outside.

### 🏆 Key Implementations
- **Zero-Touch Provisioning:** Automated OS configuration and multi-node Kubernetes cluster bootstrapping using **Ansible**.
- **100% GitOps Workflow:** No manual `kubectl apply`. Everything is templated via **Helm** and synchronized continuously by **Argo CD**.
- **Advanced Networking:** L2 Load Balancing via **MetalLB** for local access, and Zero-Trust secure external ingress via **Cloudflare Tunnel**.
- **Full Observability Stack:** Real-time metrics via **Prometheus/Grafana** and highly efficient centralized logging using **VictoriaLogs**.

*(💡 Screenshot Placeholder: ここにArgo CDのダッシュボード画面を配置して、各アプリがHealthy/Syncedになっている状態をアピールします)*
`![Argo CD Dashboard](docs/images/argocd-dashboard.png)`

---

## 🏗️ 2. Comprehensive Architecture

### 2.1 Full-Stack Infrastructure Overview

The architecture is divided into logical layers, ensuring separation of concerns, scalability, and ease of maintenance.

```mermaid
flowchart TB
    subgraph EXTERNAL["External Users"]
        INET["Internet"]
        ADMIN["Cluster Admin"]
    end

    subgraph PROVISIONING["Infrastructure as Code (Ansible)"]
        ANS["Ansible Control Node"]
        ANS -.->|"Bootstrap OS & K8s"| K8S
    end

    subgraph GITOPS["GitOps Control Plane"]
        GITHUB["GitHub Repository<br/>(Source of Truth)"]
        ARGO["Argo CD<br/>(Reconciliation Engine)"]
    end

    subgraph K8S["Multi-Node Kubernetes Cluster (Ubuntu)"]
        direction TB
        subgraph NETWORKING["Network & Ingress Layer"]
            CF["Cloudflare Tunnel<br/>(Zero-Trust Ingress)"]
            MLB["MetalLB (L2)<br/>(192.168.2.100-150)"]
        end

        subgraph APPS["Application Layer"]
            WP["WordPress (Frontend)"]
            DB["MySQL 8.0 (Backend)"]
        end

        subgraph STORAGE["Storage Layer"]
            LOCALPATH["Local Path Provisioner<br/>(Persistent Volumes)"]
        end

        subgraph OBSERVE["Observability & Monitoring Layer"]
            PROM["Prometheus"]
            GRAF["Grafana (Dashboards)"]
            ALERT["Alertmanager"]
            VLOGS["VictoriaLogs Server"]
            VCOLLECT["VictoriaLogs Collector"]
        end
    end

    %% Flow Definitions
    GITHUB -->|"Manifests / Helm"| ARGO
    ARGO -->|"Sync"| K8S
    INET -->|"Secure Tunnel"| CF
    ADMIN -->|"Local Network"| MLB
    CF --> WP
    MLB --> WP
    MLB --> GRAF
    WP --> DB
    DB --> LOCALPATH
    
    %% Monitoring Flow
    PROM -.->|"Scrape Metrics"| APPS
    VCOLLECT -.->|"Collect Logs"| APPS
    VCOLLECT -->|"Forward"| VLOGS

    style K8S fill:#f8f9fa,stroke:#326CE5,stroke-width:2px
    style GITHUB fill:#24292e,color:#fff
    style ARGO fill:#ef7b4d,color:#fff
🛠️ 3. Core Mechanisms & Workflows
3.1 Bare-Metal Provisioning with Ansible
Before GitOps takes over, the foundational infrastructure must be established. Ansible playbooks are used to transform raw Ubuntu nodes into a production-ready Kubernetes cluster.

System Preparation: Disables swap, configures sysctl for IP forwarding, and loads required kernel modules (br_netfilter, overlay).

Container Runtime: Installs and configures containerd with systemd cgroup drivers.

Cluster Bootstrapping: Automates kubeadm init on the control plane and kubeadm join on worker nodes.

CNI Deployment: Installs Calico/Flannel for intra-cluster pod communication.

3.2 The GitOps Loop (Argo CD)
This project enforces a strict GitOps methodology. Direct modifications to the cluster via CLI are considered anti-patterns.

コード スニペット
sequenceDiagram
    participant Admin as Infrastructure Admin
    participant Git as GitHub (main)
    participant Argo as Argo CD
    participant K8s as Kubernetes API

    Admin->>Git: Push Helm / YAML changes
    loop Every 3 minutes
        Argo->>Git: Poll for changes (or Webhook triggered)
    end
    Argo->>Argo: Compare desired state (Git) vs live state (K8s)
    Argo->>K8s: Apply Delta (Sync)
    K8s-->>Argo: Confirm Resource Updates
    Argo-->>Admin: Dashboard displays 'Healthy' & 'Synced'
Argo CD App of Apps Pattern:
The repository is structured using the "App of Apps" pattern, allowing a single root application to bootstrap the entire cluster's applications, namespaces, and configurations automatically.

3.3 Network & Traffic Routing
Exposing services in a bare-metal environment requires specialized tools, as cloud-native LoadBalancers are unavailable.

MetalLB: Configured in Layer 2 mode. It intercepts ARP requests and assigns IP addresses from a dedicated pool (192.168.2.100 - 150) to services of type LoadBalancer (e.g., Grafana, WordPress).

Cloudflare Tunnel (cloudflared): A lightweight daemon running in the monitoring namespace. It establishes an outbound-only connection to Cloudflare's edge, allowing secure public access to internal services without opening any inbound ports on the local router.

(💡 Screenshot Placeholder: ここにCloudflare経由で外部公開されたWordPressの実際の表示画面のキャプチャを貼ると説得力が増します)![WordPress Frontend via Cloudflare](docs/images/wordpress-live.png)

3.4 Observability: Monitoring & Centralized Logging
A robust cluster requires deep visibility into its workloads.

Metrics: The kube-prometheus-stack collects node metrics (via Node Exporter), cluster state (via kube-state-metrics), and application metrics. Grafana provides highly customizable visual dashboards.

Logging: Moved away from resource-heavy solutions like ELK to VictoriaLogs. It offers high-performance, low-RAM log ingestion. The VictoriaLogs Collector scrapes standard output from all containers and centralizes them for easy querying.

(💡 Screenshot Placeholder: ここにGrafanaでKubernetesのノードメトリクスやPodのリソース状況を可視化しているダッシュボードの画面を貼ります)![Grafana Cluster Metrics Dashboard](docs/images/grafana-dashboard.png)

📂 4. Repository Structure
Plaintext
.
├── ansible/                 # Playbooks for OS config & K8s cluster bootstrapping
├── argo-apps/               # Argo CD Application manifests (App of Apps)
├── manifests/               # Core Kubernetes YAML configurations
├── helm/                    # Custom Helm charts for localized deployments
│   ├── wordpress/
│   ├── monitoring/
│   └── networking/
└── README.md                # You are here
🚀 5. Getting Started
Prerequisites
2+ Ubuntu VMs/Bare-metal machines (Control Plane + Worker).

Ansible installed on the control machine.

A GitHub Personal Access Token (for Argo CD private repo access).

Deployment Steps
Provision Infrastructure: Run the Ansible playbook to bootstrap the cluster.

Bash
ansible-playbook -i hosts k8s-setup.yml
Install Argo CD:

Bash
kubectl create namespace argocd
kubectl apply -n argocd -f [https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml](https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml)
Apply the Root Application:

Bash
kubectl apply -f argo-apps/root-app.yaml
