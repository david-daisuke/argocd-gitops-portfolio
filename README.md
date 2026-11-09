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

The project demonstrates practical experience in designing, deploying, and operating a multi-node Kubernetes environment using GitOps principles.

The primary objectives are to demonstrate skills in:

- Kubernetes cluster administration and workload management
- GitOps-based infrastructure management with Argo CD
- Declarative application deployment using Kubernetes manifests
- Helm-based application installation and lifecycle management
- Container networking and external service exposure
- Infrastructure monitoring and metrics collection
- Centralized log collection and storage
- Persistent storage for stateful applications
- Secure external connectivity using Cloudflare Tunnel
- Kubernetes troubleshooting and operational practices

The Git repository serves as the source of truth for the resources managed by Argo CD.

Changes committed to Git are automatically reconciled with the desired state of the Kubernetes cluster.

### Platform Components

| Component | Purpose |
|---|---|
| Kubernetes | Container orchestration platform |
| Argo CD | GitOps continuous delivery |
| Helm | Application packaging and deployment |
| WordPress | Self-hosted web application |
| MySQL 8.0 | WordPress database |
| MetalLB | LoadBalancer implementation for bare-metal Kubernetes |
| Prometheus | Metrics collection and monitoring |
| Grafana | Metrics visualization and dashboards |
| kube-state-metrics | Kubernetes object state metrics |
| Node Exporter | Host-level metrics |
| Alertmanager | Alert handling and notifications |
| VictoriaLogs | Centralized log storage |
| VictoriaLogs Collector | Log collection and forwarding |
| Cloudflare Tunnel | Secure external connectivity |

---

## 2. Architecture

### 2.1 High-Level Infrastructure Architecture

The following diagram illustrates the major components and their relationships within the Kubernetes platform.

```mermaid
flowchart TB
    USER["Users / Administrators"]
    GITHUB["GitHub Repository<br/>Source of Truth"]
    ARGO["Argo CD<br/>GitOps Controller"]

    subgraph K8S["Kubernetes Cluster"]
        subgraph APP["Application Layer"]
            WP["WordPress"]
            DB["MySQL 8.0"]
            WPVOL[("WordPress Persistent Storage")]
            DBVOL[("MySQL Persistent Storage")]

            WP --> DB
            WP --> WPVOL
            DB --> DBVOL
        end

        subgraph OBS["Observability Layer"]
            PROM["Prometheus"]
            GRAF["Grafana"]
            ALERT["Alertmanager"]
            KSM["kube-state-metrics"]
            NODE["Node Exporter"]
            VLOG["VictoriaLogs Server"]
            VLC["VictoriaLogs Collector"]

            KSM --> PROM
            NODE --> PROM
            PROM --> GRAF
            PROM --> ALERT
            VLC --> VLOG
        end

        subgraph NET["Networking Layer"]
            METALLB["MetalLB"]
            LB["LoadBalancer Services"]
            TUNNEL["Cloudflare Tunnel Connector"]

            METALLB --> LB
        end
    end

    GITHUB -->|"Repository synchronization"| ARGO
    ARGO -->|"Reconciliation"| K8S

    USER -->|"Application access"| WP
    USER -->|"Dashboard access"| GRAF

    LB --> WP
    LB --> GRAF
    TUNNEL --> WP
    TUNNEL --> GRAF

    style K8S fill:#f5f7fa,stroke:#326CE5,stroke-width:2px
    style GITHUB fill:#24292e,color:#ffffff
    style ARGO fill:#ef7b4d,color:#ffffff
    style VLOG fill:#5b6abf,color:#ffffff
2.2 GitOps Deployment Workflow
Argo CD monitors the Git repository and continuously reconciles the Kubernetes cluster with the desired state defined in Git.

The deployment workflow follows these steps:

A developer modifies Kubernetes manifests or Helm Application definitions.

Changes are committed and pushed to GitHub.

Argo CD detects repository changes.

Argo CD compares the desired state with the actual cluster state.

Argo CD applies the required changes.

Kubernetes creates or updates the corresponding resources.

Argo CD reports synchronization and health status.


mermaid
flowchart LR
    A["Developer"] -->|"Git commit / push"| B["GitHub Repository"]
    B -->|"Repository monitoring"| C["Argo CD"]
    C -->|"Reconciliation"| D["Kubernetes API"]
    D --> E["Kubernetes Resources"]
    E -->|"Health and Sync Status"| C
The main GitOps configuration is defined in portfolio-apps.yaml.

Important synchronization settings:

Setting

Description

Automated Sync

Automatically applies changes from Git

Prune

Removes managed resources deleted from Git

Self-Heal

Reconciles manual changes made directly to the cluster

CreateNamespace

Creates destination namespaces when configured

2.3 Network and Service Exposure
The platform uses MetalLB for LAN-facing LoadBalancer services and Cloudflare Tunnel for external connectivity.

These are separate network paths.

LAN access through MetalLB:


mermaid
flowchart LR
    A["LAN Client"] --> B["MetalLB External IP"]
    B --> C["Kubernetes LoadBalancer Service"]
    C --> D["Application Pod"]
External access through Cloudflare Tunnel:


mermaid
flowchart LR
    A["External User"] --> B["Cloudflare Network"]
    B --> C["Cloudflare Tunnel"]
    C --> D["cloudflared Connector"]
    D --> E["Kubernetes Service"]
    E --> F["Application Pod"]
The actual public hostname, Cloudflare Tunnel ingress rules, and routing configuration are maintained outside the Deployment manifest.

3. Repository Structure
The repository is organized into root-level Argo CD Application definitions and Kubernetes manifests.


text
argocd-gitops-portfolio/
├── README.md
├── portfolio-apps.yaml
├── prometheus-argocd-app.yaml
├── broken-pod.yaml
└── manifests/
    ├── cloudflared-deployment.yaml
    ├── grafana-lb-service.yaml
    ├── metallb-config.yaml
    ├── victoria-logs-collector.yaml
    ├── victoria-logs-nodeport.yaml
    ├── victoria-logs-server.yaml
    └── wordpress-stack.yaml
3.1 Root-Level Manifests
File

Purpose

portfolio-apps.yaml

Main Argo CD Application that deploys resources from the manifests/ directory

prometheus-argocd-app.yaml

Deploys kube-prometheus-stack using Helm

broken-pod.yaml

Intentionally misconfigured Pod manifest for troubleshooting practice

README.md

Project documentation

3.2 Application Manifests
File

Resource / Application

Purpose

cloudflared-deployment.yaml

Deployment

Runs Cloudflare Tunnel connectors

grafana-lb-service.yaml

Service

Exposes Grafana through LoadBalancer

metallb-config.yaml

IPAddressPool / L2Advertisement

Configures MetalLB address allocation

victoria-logs-collector.yaml

Argo CD Application

Deploys VictoriaLogs Collector using Helm

victoria-logs-nodeport.yaml

Service

Exposes VictoriaLogs through NodePort

victoria-logs-server.yaml

Argo CD Application

Deploys VictoriaLogs Server using Helm

wordpress-stack.yaml

PVCs / Deployments / Services

Deploys WordPress and MySQL

4. GitOps Configuration
4.1 Main Argo CD Application
File: portfolio-apps.yaml

This manifest defines the main Argo CD Application responsible for managing the Kubernetes resources stored in the manifests/ directory.

Configuration

Value

Application name

portfolio-apps

Namespace

argocd

Repository

https://github.com/david-daisuke/argocd-gitops-portfolio.git 

Target revision

main

Manifest path

manifests

Destination namespace

default

Automated Sync

Enabled

Prune

Enabled

Self-Heal

Enabled

Argo CD monitors the repository and automatically reconciles the cluster when changes are pushed to GitHub.

4.2 Monitoring Stack Application
File: prometheus-argocd-app.yaml

This Application deploys the Prometheus monitoring stack using the Prometheus Community Helm chart.

Configuration

Value

Application name

portfolio-monitoring-stack

Helm repository

https://prometheus-community.github.io/helm-charts 

Chart

kube-prometheus-stack

Chart version

62.7.0

Namespace

monitoring

Automated Sync

Enabled

Prune

Enabled

Self-Heal

Enabled

The Helm chart provides the core monitoring components, including Prometheus, Grafana, Alertmanager, kube-state-metrics, and Node Exporter.

4.3 VictoriaLogs Applications
VictoriaLogs Server and VictoriaLogs Collector are managed as separate Argo CD Applications.

This separation provides independent deployment and lifecycle management.

Application

Responsibility

VictoriaLogs Server

Stores and serves collected logs

VictoriaLogs Collector

Collects and forwards logs to the server

Both Applications are defined under the manifests/ directory.

5. Application and Infrastructure Details
5.1 WordPress and MySQL
WordPress runs as a Kubernetes Deployment with a separate MySQL 8.0 database.

Configuration file: manifests/wordpress-stack.yaml


mermaid
flowchart TB
    USER["User"]
    SVC["WordPress Service"]
    WP["WordPress Deployment"]
    WPVOL[("wp-content-pvc")]
    DBSVC["wordpress-mysql-service"]
    DB["MySQL 8.0 Deployment"]
    DBVOL[("wp-mysql-pvc")]
    SECRET["wp-db-secret"]

    USER --> SVC
    SVC --> WP
    WP --> WPVOL
    WP --> DBSVC
    DBSVC --> DB
    DB --> DBVOL
    SECRET --> DB
    SECRET --> WP
Kubernetes Resources
Resource

Name

Configuration

PersistentVolumeClaim

wp-mysql-pvc

10Gi, ReadWriteOnce

Deployment

wordpress-mysql

MySQL 8.0

Service

wordpress-mysql-service

TCP 3306

PersistentVolumeClaim

wp-content-pvc

10Gi, ReadWriteOnce

Deployment

wordpress

WordPress container

Service

wordpress-service

LoadBalancer, TCP 80

Secret

wp-db-secret

Database credentials

The WordPress container connects to MySQL through the internal Kubernetes Service:

wordpress-mysql-service

PersistentVolumeClaims provide persistent storage for the database and WordPress content.

Database credentials are referenced through a Kubernetes Secret rather than being embedded directly in the application configuration.

5.2 MetalLB
MetalLB provides LoadBalancer functionality for this bare-metal Kubernetes environment.

Configuration file: manifests/metallb-config.yaml

Resource

Name

Value

IPAddressPool

default-pool

192.168.2.100-192.168.2.150

L2Advertisement

default-l2

Advertises the address pool


mermaid
flowchart LR
    A["LAN Client"] --> B["LoadBalancer IP"]
    B --> C["MetalLB"]
    C --> D["Kubernetes Service"]
    D --> E["Application Pod"]
MetalLB allocates external IP addresses to Kubernetes Services of type LoadBalancer.

This allows applications running inside the cluster to be accessed from the local network without requiring a cloud-provider LoadBalancer implementation.

5.3 Cloudflare Tunnel
Cloudflare Tunnel provides an additional path for external connectivity without directly exposing the cluster through inbound public network ports.

Configuration file: manifests/cloudflared-deployment.yaml

Setting

Value

Deployment

cloudflared

Namespace

monitoring

Replicas

2

Container image

cloudflare/cloudflared:latest

Tunnel mode

tunnel --no-autoupdate run

Authentication

Kubernetes Secret cloudflared-secret

Secret key

tunnel-token

CPU request

100m

Memory request

128Mi

CPU limit

300m

Memory limit

256Mi

The tunnel token is referenced from a Kubernetes Secret instead of being embedded directly in the Deployment manifest.

The Cloudflare Tunnel connector establishes outbound connectivity to Cloudflare.

Public hostname configuration and ingress routing are maintained separately from the Deployment manifest.

5.4 Prometheus and Grafana
The monitoring stack is deployed using the Prometheus Community kube-prometheus-stack Helm chart.

Configuration file: prometheus-argocd-app.yaml

Component

Purpose

Prometheus

Collects and stores metrics

Grafana

Visualizes metrics through dashboards

Alertmanager

Handles alert notifications

kube-state-metrics

Exposes Kubernetes object state metrics

Node Exporter

Exposes node-level operating system metrics

Prometheus Operator

Manages Prometheus-related resources

Helm Configuration
Setting

Value

Repository

https://prometheus-community.github.io/helm-charts 

Chart

kube-prometheus-stack

Version

62.7.0

Namespace

monitoring

Grafana is exposed through the Service defined in:

manifests/grafana-lb-service.yaml

The Service uses type LoadBalancer and forwards TCP port 80 to Grafana's container port 3000.

Prometheus collects metrics from Kubernetes components and exporters, while Grafana provides a centralized interface for visualization and monitoring.

5.5 VictoriaLogs
VictoriaLogs provides centralized log storage for the Kubernetes environment.

The deployment is divided into two independent Argo CD-managed Helm Applications:

VictoriaLogs Server

VictoriaLogs Collector

This architecture separates log ingestion from log storage.

VictoriaLogs Server
Configuration file: manifests/victoria-logs-server.yaml

Setting

Value

Helm chart

victoria-logs-single

Chart version

0.13.10

Release name

vls

Namespace

logging

Retention period

7 days

Storage class

local-path

Persistent storage

10Gi

Memory limit

1Gi

VictoriaLogs Collector
Configuration file: manifests/victoria-logs-collector.yaml

Setting

Value

Helm chart

victoria-logs-collector

Chart version

0.3.7

Release name

vlc

Namespace

logging

Destination

VictoriaLogs Server

CPU request

100m

Memory request

128Mi

CPU limit

300m

Memory limit

256Mi

The collector forwards logs to the VictoriaLogs Server through the following internal Kubernetes Service URL:

http://vls-victoria-logs-single-server.logging.svc.cluster.local:9428

The victoria-logs-nodeport.yaml manifest exposes the VictoriaLogs Server through NodePort 30428.

Log Collection Architecture

mermaid
flowchart TB
    NODES["Kubernetes Nodes"]
    PODS["Application and System Pods"]
    COLLECTOR["VictoriaLogs Collector"]
    SERVER["VictoriaLogs Server"]
    STORAGE[("Persistent Volume")]
    REMOTE["Remote Write Sources"]

    NODES --> PODS
    PODS --> COLLECTOR
    COLLECTOR --> SERVER
    REMOTE --> SERVER
    SERVER --> STORAGE
The collector is responsible for forwarding logs to the server, while VictoriaLogs Server stores the received log data on persistent storage.

6. Network Exposure
The repository defines multiple ways to access applications running inside the cluster.

Application

Kubernetes Service

Port

WordPress

LoadBalancer

80

Grafana

LoadBalancer

80

VictoriaLogs

NodePort

30428

Network Access Methods
Method

Purpose

MetalLB

Provides external IP allocation for LoadBalancer Services

Kubernetes ClusterIP

Provides internal service-to-service communication

NodePort

Exposes VictoriaLogs through a node port

Cloudflare Tunnel

Provides an additional external connectivity path

MetalLB provides LoadBalancer IP allocation for services in the bare-metal cluster.

Cloudflare Tunnel provides external connectivity through an outbound tunnel connection.

The actual public hostname and tunnel ingress routing are configured outside the Deployment manifest.

7. Namespace Organization
The platform uses separate namespaces to organize infrastructure components and applications.

Namespace

Components

argocd

Argo CD

default

WordPress and MySQL

monitoring

Prometheus, Grafana, Alertmanager, Cloudflare Tunnel

logging

VictoriaLogs Server and Collector

metallb-system

MetalLB

kube-system

Kubernetes system components

local-path-storage

Local Path Provisioner

Namespace separation helps organize workloads and provides a foundation for applying namespace-specific policies and access controls.

8. Operations and Troubleshooting
The following commands are useful for operating and troubleshooting the Kubernetes platform.

8.1 Check Cluster Nodes

bash
kubectl get nodes -o wide
8.2 Check Namespaces

bash
kubectl get namespaces
8.3 Check All Pods

bash
kubectl get pods -A -o wide
8.4 Check Services

bash
kubectl get services -A
8.5 Check PersistentVolumeClaims

bash
kubectl get pvc -A
8.6 Check Argo CD Applications

bash
kubectl get applications -n argocd
8.7 Inspect an Argo CD Application

bash
kubectl describe application portfolio-apps -n argocd
8.8 Inspect a Pod

bash
kubectl describe pod <pod-name> -n <namespace>
8.9 Check Recent Events

bash
kubectl get events -A --sort-by=.metadata.creationTimestamp
8.10 Check Deployment Status

bash
kubectl get deployments -A
8.11 Check Persistent Volumes

bash
kubectl get pv
These commands provide a starting point for investigating workload health, service exposure, storage allocation, and GitOps synchronization issues.

9. Troubleshooting Exercise
The repository includes broken-pod.yaml, an intentionally misconfigured Pod manifest.

The manifest uses an invalid Nginx image tag:


yaml
image: nginx:typo-version
This file is stored outside the manifests/ directory and is therefore not automatically deployed by the main Argo CD Application.

It can be used to practice identifying and troubleshooting image pull failures.

9.1 Deploy the Test Pod

bash
kubectl apply -f broken-pod.yaml
9.2 Inspect the Pod

bash
kubectl get pods
kubectl describe pod broken-nginx
9.3 Inspect Events

bash
kubectl get events --sort-by=.metadata.creationTimestamp
The invalid image tag should result in an image pull failure, such as ErrImagePull or ImagePullBackOff.

9.4 Clean Up
Delete the test resource after completing the exercise:


bash
kubectl delete -f broken-pod.yaml
This exercise demonstrates basic Kubernetes troubleshooting using Pod status, container events, and image pull diagnostics.

10. Security and Production Considerations
This repository is a personal infrastructure portfolio and learning environment.

It demonstrates practical infrastructure management but should not be considered production-ready without additional security, reliability, and operational improvements.

Recommended improvements include:

Store credentials in Kubernetes Secrets or an external secret manager.

Avoid committing passwords, API tokens, and tunnel credentials to Git.

Avoid using default or easily guessed passwords.

Pin container images to specific versions or immutable digests instead of relying on latest.

Configure resource requests and limits for all workloads.

Review network exposure and restrict access to administrative services.

Configure backups and test recovery procedures for persistent data.

Consider high availability and storage replication where required.

Review TLS configuration for externally accessible services.

Implement Kubernetes NetworkPolicies where appropriate.

Apply least-privilege RBAC permissions.

Regularly review container images and software dependencies.

Important security note:

Review prometheus-argocd-app.yaml before making this repository public.

If it contains a hardcoded Grafana password, replace that configuration with a secure Secret-based approach.

Never commit actual credentials, private keys, API tokens, or Cloudflare Tunnel tokens to a public Git repository.

11. Current Deployment Status
The following Argo CD Applications were observed in the cluster during the documentation update.

Application

Sync Status

Health Status

portfolio-apps

Synced

Healthy

portfolio-monitoring-stack

Synced

Healthy

victoria-logs-collector

Synced

Healthy

victoria-logs-server

Synced

Healthy

These statuses represent the state of the cluster at the time of documentation.

They may change as infrastructure resources, workloads, or configurations evolve.

For the latest deployment status, check Argo CD directly or run:


bash
kubectl get applications -n argocd
12. Future Improvements
Potential future enhancements for this project include:

GitOps and Automation
Add automated Kubernetes manifest validation.

Introduce Kustomize overlays for environment-specific configurations.

Add CI workflows using GitHub Actions.

Implement automated deployment validation.

Monitoring and Observability
Add custom Grafana dashboards.

Improve Prometheus alert rules.

Expand centralized log collection.

Introduce alert notification integrations.

Security and Reliability
Improve secrets management.

Add Kubernetes NetworkPolicies.

Implement workload security controls.

Add automated backup and restore testing.

Document disaster recovery procedures.

Improve storage reliability and availability.

These improvements would further strengthen the platform's maintainability, security, and operational maturity.

13. Author
David Daisuke

GitHub: david-daisuke 

This repository demonstrates hands-on experience with:

Kubernetes administration

GitOps and Argo CD

Helm-based application deployment

Infrastructure networking with MetalLB

External connectivity using Cloudflare Tunnel

Monitoring with Prometheus and Grafana

Centralized logging with VictoriaLogs

Persistent storage and stateful workloads

Kubernetes troubleshooting and operations

The goal of this project is to demonstrate the practical design, deployment, and operation of a self-hosted Kubernetes infrastructure platform using declarative configuration and GitOps principles.
