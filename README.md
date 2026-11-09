# Kubernetes GitOps Infrastructure Portfolio

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?logo=kubernetes&logoColor=white)
![Argo CD](https://img.shields.io/badge/Argo_CD-EF7B4D?logo=argo&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?logo=grafana&logoColor=white)

A self-managed, multi-node Kubernetes environment managed through GitOps.

GitHub acts as the source of truth. Argo CD continuously reconciles
the cluster with the desired configuration stored in this repository.

## 1. Platform Overview

| Component | Purpose |
|---|---|
| Kubernetes | Container orchestration |
| Argo CD | GitOps continuous delivery |
| Helm | Application packaging |
| WordPress | Web application |
| MySQL 8.0 | WordPress database |
| MetalLB | Bare-metal LoadBalancer |
| Prometheus | Metrics collection |
| Grafana | Metrics visualization |
| Alertmanager | Alert management |
| kube-state-metrics | Kubernetes state metrics |
| Node Exporter | Host metrics |
| VictoriaLogs | Centralized log storage |
| VictoriaLogs Collector | Log collection |
| Cloudflare Tunnel | External connectivity |

## 2. Architecture

### 2.1 High-Level Infrastructure Architecture

```mermaid
flowchart TB
    G["GitHub"] --> A["Argo CD"]
    A --> K["Kubernetes"]
    U["Users"] --> M["MetalLB"]
    U --> C["Cloudflare Tunnel"]
    M --> W["WordPress"]
    M --> F["Grafana"]
    C --> W
    W --> D["MySQL"]
    P["Prometheus"] --> F
    L["Log Collector"] --> V["VictoriaLogs"]


2.2 GitOps Workflow
#chatgpt-mermaid-_r_30s_{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;fill:rgb(13, 13, 13);}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#chatgpt-mermaid-_r_30s_ .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-_r_30s_ .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-_r_30s_ .error-icon{fill:rgb(243, 243, 243);}#chatgpt-mermaid-_r_30s_ .error-text{fill:rgb(13, 13, 13);stroke:rgb(13, 13, 13);}#chatgpt-mermaid-_r_30s_ .edge-thickness-normal{stroke-width:1px;}#chatgpt-mermaid-_r_30s_ .edge-thickness-thick{stroke-width:3.5px;}#chatgpt-mermaid-_r_30s_ .edge-pattern-solid{stroke-dasharray:0;}#chatgpt-mermaid-_r_30s_ .edge-thickness-invisible{stroke-width:0;fill:none;}#chatgpt-mermaid-_r_30s_ .edge-pattern-dashed{stroke-dasharray:3;}#chatgpt-mermaid-_r_30s_ .edge-pattern-dotted{stroke-dasharray:2;}#chatgpt-mermaid-_r_30s_ .marker{fill:rgb(143, 143, 143);stroke:rgb(143, 143, 143);}#chatgpt-mermaid-_r_30s_ .marker.cross{stroke:rgb(143, 143, 143);}#chatgpt-mermaid-_r_30s_ svg{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;}#chatgpt-mermaid-_r_30s_ p{margin:0;}#chatgpt-mermaid-_r_30s_ .label{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";color:rgb(13, 13, 13);}#chatgpt-mermaid-_r_30s_ .cluster-label text{fill:rgb(13, 13, 13);}#chatgpt-mermaid-_r_30s_ .cluster-label span{color:rgb(13, 13, 13);}#chatgpt-mermaid-_r_30s_ .cluster-label span p{background-color:transparent;}#chatgpt-mermaid-_r_30s_ .label text,#chatgpt-mermaid-_r_30s_ span{fill:rgb(13, 13, 13);color:rgb(13, 13, 13);}#chatgpt-mermaid-_r_30s_ .node rect,#chatgpt-mermaid-_r_30s_ .node circle,#chatgpt-mermaid-_r_30s_ .node ellipse,#chatgpt-mermaid-_r_30s_ .node polygon,#chatgpt-mermaid-_r_30s_ .node path{fill:rgb(222, 234, 251);stroke:rgb(83, 154, 248);stroke-width:1px;}#chatgpt-mermaid-_r_30s_ .rough-node .label text,#chatgpt-mermaid-_r_30s_ .node .label text,#chatgpt-mermaid-_r_30s_ .image-shape .label,#chatgpt-mermaid-_r_30s_ .icon-shape .label{text-anchor:middle;}#chatgpt-mermaid-_r_30s_ .node .katex path{fill:#000;stroke:#000;stroke-width:1px;}#chatgpt-mermaid-_r_30s_ .rough-node .label,#chatgpt-mermaid-_r_30s_ .node .label,#chatgpt-mermaid-_r_30s_ .image-shape .label,#chatgpt-mermaid-_r_30s_ .icon-shape .label{text-align:center;}#chatgpt-mermaid-_r_30s_ .node.clickable{cursor:pointer;}#chatgpt-mermaid-_r_30s_ .root .anchor path{fill:rgb(143, 143, 143)!important;stroke-width:0;stroke:rgb(143, 143, 143);}#chatgpt-mermaid-_r_30s_ .arrowheadPath{fill:rgb(143, 143, 143);}#chatgpt-mermaid-_r_30s_ .edgePath .path{stroke:rgb(143, 143, 143);stroke-width:1px;}#chatgpt-mermaid-_r_30s_ .flowchart-link{stroke:rgb(143, 143, 143);fill:none;}#chatgpt-mermaid-_r_30s_ .edgeLabel{background-color:rgb(252, 252, 252);text-align:center;}#chatgpt-mermaid-_r_30s_ .edgeLabel p{background-color:rgb(252, 252, 252);}#chatgpt-mermaid-_r_30s_ .edgeLabel rect{opacity:0.5;background-color:rgb(252, 252, 252);fill:rgb(252, 252, 252);}#chatgpt-mermaid-_r_30s_ .labelBkg{background-color:rgba(252, 252, 252, 0.5);}#chatgpt-mermaid-_r_30s_ .cluster rect{fill:rgb(243, 243, 243);stroke:rgba(0, 0, 0, 0.1);stroke-width:1px;}#chatgpt-mermaid-_r_30s_ .cluster text{fill:rgb(13, 13, 13);}#chatgpt-mermaid-_r_30s_ .cluster span{color:rgb(13, 13, 13);}#chatgpt-mermaid-_r_30s_ div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:12px;background:rgb(243, 243, 243);border:1px solid rgba(0, 0, 0, 0.1);border-radius:2px;pointer-events:none;z-index:100;}#chatgpt-mermaid-_r_30s_ .flowchartTitleText{text-anchor:middle;font-size:18px;fill:rgb(13, 13, 13);}#chatgpt-mermaid-_r_30s_ rect.text{fill:none;stroke-width:0;}#chatgpt-mermaid-_r_30s_ .icon-shape,#chatgpt-mermaid-_r_30s_ .image-shape{background-color:rgb(252, 252, 252);text-align:center;}#chatgpt-mermaid-_r_30s_ .icon-shape p,#chatgpt-mermaid-_r_30s_ .image-shape p{background-color:rgb(252, 252, 252);padding:2px;}#chatgpt-mermaid-_r_30s_ .icon-shape .label rect,#chatgpt-mermaid-_r_30s_ .image-shape .label rect{opacity:0.5;background-color:rgb(252, 252, 252);fill:rgb(252, 252, 252);}#chatgpt-mermaid-_r_30s_ .label-icon{display:inline-block;height:1em;overflow:visible;vertical-align:-0.125em;}#chatgpt-mermaid-_r_30s_ .node .label-icon path{fill:currentColor;stroke:revert;stroke-width:revert;}#chatgpt-mermaid-_r_30s_ .node .neo-node{stroke:rgb(83, 154, 248);}#chatgpt-mermaid-_r_30s_ [data-look="neo"].node rect,#chatgpt-mermaid-_r_30s_ [data-look="neo"].cluster rect,#chatgpt-mermaid-_r_30s_ [data-look="neo"].node polygon{stroke:url(#chatgpt-mermaid-_r_30s_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_30s_ [data-look="neo"].swimlane.cluster rect{filter:none;}#chatgpt-mermaid-_r_30s_ [data-look="neo"].node path{stroke:url(#chatgpt-mermaid-_r_30s_-gradient);stroke-width:1px;}#chatgpt-mermaid-_r_30s_ [data-look="neo"].node .outer-path{filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_30s_ [data-look="neo"].node .neo-line path{stroke:rgb(83, 154, 248);filter:none;}#chatgpt-mermaid-_r_30s_ [data-look="neo"].node circle{stroke:url(#chatgpt-mermaid-_r_30s_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_30s_ [data-look="neo"].node circle .state-start{fill:#000000;}#chatgpt-mermaid-_r_30s_ [data-look="neo"].icon-shape .icon{fill:url(#chatgpt-mermaid-_r_30s_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_30s_ [data-look="neo"].icon-shape .icon-neo path{stroke:url(#chatgpt-mermaid-_r_30s_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_30s_ .node text{font-size:14px;font-weight:600;letter-spacing:normal;fill:rgb(0, 79, 153);}#chatgpt-mermaid-_r_30s_ .edgeLabels text{font-size:13px;font-weight:600;letter-spacing:-0.08px;fill:rgb(0, 79, 153);}#chatgpt-mermaid-_r_30s_ .node tspan[font-weight="normal"],#chatgpt-mermaid-_r_30s_ .edgeLabels tspan[font-weight="normal"]{font-weight:600;}#chatgpt-mermaid-_r_30s_ .edgeLabel .label rect{opacity:1;rx:13px;ry:13px;fill:rgb(245, 250, 255);stroke:rgb(206, 219, 229);stroke-width:1px;}#chatgpt-mermaid-_r_30s_ .node rect,#chatgpt-mermaid-_r_30s_ .node circle,#chatgpt-mermaid-_r_30s_ .node ellipse,#chatgpt-mermaid-_r_30s_ .node polygon,#chatgpt-mermaid-_r_30s_ .node path{fill:rgb(229, 243, 255);stroke:rgba(0, 0, 0, 0.1);stroke-width:1px;}#chatgpt-mermaid-_r_30s_ .node rect{rx:16px;ry:16px;}#chatgpt-mermaid-_r_30s_ .mermaid-decision .label-container{fill:rgb(245, 250, 255);stroke:rgb(206, 219, 229);stroke-dasharray:2,2;}#chatgpt-mermaid-_r_30s_ .edgePaths .flowchart-link{stroke:rgb(143, 143, 143);stroke-width:1px;stroke-linecap:round;stroke-linejoin:round;}#chatgpt-mermaid-_r_30s_ .marker{fill:rgb(143, 143, 143);stroke:rgb(143, 143, 143);}#chatgpt-mermaid-_r_30s_ :root{--mermaid-font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";}DeveloperGitHubArgo CDKubernetes





Changes pushed to GitHub are detected by Argo CD.
Argo CD applies the desired configuration to the cluster.

The main Application uses automated synchronization, pruning,
and self-healing.
3. Repository Structure
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


4. Manifest Reference
Root directory
File	Description
portfolio-apps.yaml	Main Argo CD Application
prometheus-argocd-app.yaml	Prometheus monitoring stack
broken-pod.yaml	Troubleshooting exercise

manifests directory
File	Description
cloudflared-deployment.yaml	Cloudflare Tunnel Deployment
grafana-lb-service.yaml	Grafana LoadBalancer Service
metallb-config.yaml	MetalLB address pool and L2 advertisement
victoria-logs-collector.yaml	VictoriaLogs Collector Application
victoria-logs-nodeport.yaml	VictoriaLogs NodePort Service
victoria-logs-server.yaml	VictoriaLogs Server Application
wordpress-stack.yaml	WordPress and MySQL resources

5. GitOps Applications
5.1 Main Application
File: portfolio-apps.yaml
Setting	Value
Application	portfolio-apps
Namespace	argocd
Git branch	main
Manifest path	manifests
Automated sync	Enabled
Prune	Enabled
Self-heal	Enabled

5.2 Monitoring Stack
File: prometheus-argocd-app.yaml
Setting	Value
Application	portfolio-monitoring-stack
Helm chart	kube-prometheus-stack
Chart version	62.7.0
Namespace	monitoring

The stack includes Prometheus, Grafana, Alertmanager,
kube-state-metrics, Node Exporter, and Prometheus Operator.
5.3 VictoriaLogs
Two separate Argo CD Applications manage the logging platform.
Component	Helm chart	Version
Server	victoria-logs-single	0.13.10
Collector	victoria-logs-collector	0.3.7

The server uses a 10Gi PersistentVolumeClaim and a 7-day
retention period. The collector forwards logs to the server
inside the Kubernetes cluster.
6. WordPress and MySQL
File: manifests/wordpress-stack.yaml
Resource	Name	Configuration
PVC	wp-mysql-pvc	10Gi
Deployment	wordpress-mysql	MySQL 8.0
Service	wordpress-mysql-service	TCP 3306
PVC	wp-content-pvc	10Gi
Deployment	wordpress	WordPress
Service	wordpress-service	LoadBalancer
Secret	wp-db-secret	Database credentials

WordPress connects to MySQL through the internal Kubernetes Service.
PersistentVolumeClaims provide storage for database and website data.
7. Networking
MetalLB
File: manifests/metallb-config.yaml
Setting	Value
IPAddressPool	default-pool
Address range	192.168.2.100-192.168.2.150
L2Advertisement	default-l2

MetalLB provides LoadBalancer IP addresses for bare-metal services.
Service exposure
Application	Service type	Port
WordPress	LoadBalancer	80
Grafana	LoadBalancer	80
VictoriaLogs	NodePort	30428

Cloudflare Tunnel
File: manifests/cloudflared-deployment.yaml
Setting	Value
Deployment	cloudflared
Replicas	2
Image	cloudflare/cloudflared:latest
Authentication	Secret cloudflared-secret
Secret key	tunnel-token

The tunnel token is obtained from a Kubernetes Secret.
Public hostname and ingress routing are configured separately.
8. Logging Architecture
#chatgpt-mermaid-_r_317_{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;fill:rgb(13, 13, 13);}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#chatgpt-mermaid-_r_317_ .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-_r_317_ .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#chatgpt-mermaid-_r_317_ .error-icon{fill:rgb(243, 243, 243);}#chatgpt-mermaid-_r_317_ .error-text{fill:rgb(13, 13, 13);stroke:rgb(13, 13, 13);}#chatgpt-mermaid-_r_317_ .edge-thickness-normal{stroke-width:1px;}#chatgpt-mermaid-_r_317_ .edge-thickness-thick{stroke-width:3.5px;}#chatgpt-mermaid-_r_317_ .edge-pattern-solid{stroke-dasharray:0;}#chatgpt-mermaid-_r_317_ .edge-thickness-invisible{stroke-width:0;fill:none;}#chatgpt-mermaid-_r_317_ .edge-pattern-dashed{stroke-dasharray:3;}#chatgpt-mermaid-_r_317_ .edge-pattern-dotted{stroke-dasharray:2;}#chatgpt-mermaid-_r_317_ .marker{fill:rgb(143, 143, 143);stroke:rgb(143, 143, 143);}#chatgpt-mermaid-_r_317_ .marker.cross{stroke:rgb(143, 143, 143);}#chatgpt-mermaid-_r_317_ svg{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:16px;}#chatgpt-mermaid-_r_317_ p{margin:0;}#chatgpt-mermaid-_r_317_ .label{font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";color:rgb(13, 13, 13);}#chatgpt-mermaid-_r_317_ .cluster-label text{fill:rgb(13, 13, 13);}#chatgpt-mermaid-_r_317_ .cluster-label span{color:rgb(13, 13, 13);}#chatgpt-mermaid-_r_317_ .cluster-label span p{background-color:transparent;}#chatgpt-mermaid-_r_317_ .label text,#chatgpt-mermaid-_r_317_ span{fill:rgb(13, 13, 13);color:rgb(13, 13, 13);}#chatgpt-mermaid-_r_317_ .node rect,#chatgpt-mermaid-_r_317_ .node circle,#chatgpt-mermaid-_r_317_ .node ellipse,#chatgpt-mermaid-_r_317_ .node polygon,#chatgpt-mermaid-_r_317_ .node path{fill:rgb(222, 234, 251);stroke:rgb(83, 154, 248);stroke-width:1px;}#chatgpt-mermaid-_r_317_ .rough-node .label text,#chatgpt-mermaid-_r_317_ .node .label text,#chatgpt-mermaid-_r_317_ .image-shape .label,#chatgpt-mermaid-_r_317_ .icon-shape .label{text-anchor:middle;}#chatgpt-mermaid-_r_317_ .node .katex path{fill:#000;stroke:#000;stroke-width:1px;}#chatgpt-mermaid-_r_317_ .rough-node .label,#chatgpt-mermaid-_r_317_ .node .label,#chatgpt-mermaid-_r_317_ .image-shape .label,#chatgpt-mermaid-_r_317_ .icon-shape .label{text-align:center;}#chatgpt-mermaid-_r_317_ .node.clickable{cursor:pointer;}#chatgpt-mermaid-_r_317_ .root .anchor path{fill:rgb(143, 143, 143)!important;stroke-width:0;stroke:rgb(143, 143, 143);}#chatgpt-mermaid-_r_317_ .arrowheadPath{fill:rgb(143, 143, 143);}#chatgpt-mermaid-_r_317_ .edgePath .path{stroke:rgb(143, 143, 143);stroke-width:1px;}#chatgpt-mermaid-_r_317_ .flowchart-link{stroke:rgb(143, 143, 143);fill:none;}#chatgpt-mermaid-_r_317_ .edgeLabel{background-color:rgb(252, 252, 252);text-align:center;}#chatgpt-mermaid-_r_317_ .edgeLabel p{background-color:rgb(252, 252, 252);}#chatgpt-mermaid-_r_317_ .edgeLabel rect{opacity:0.5;background-color:rgb(252, 252, 252);fill:rgb(252, 252, 252);}#chatgpt-mermaid-_r_317_ .labelBkg{background-color:rgba(252, 252, 252, 0.5);}#chatgpt-mermaid-_r_317_ .cluster rect{fill:rgb(243, 243, 243);stroke:rgba(0, 0, 0, 0.1);stroke-width:1px;}#chatgpt-mermaid-_r_317_ .cluster text{fill:rgb(13, 13, 13);}#chatgpt-mermaid-_r_317_ .cluster span{color:rgb(13, 13, 13);}#chatgpt-mermaid-_r_317_ div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";font-size:12px;background:rgb(243, 243, 243);border:1px solid rgba(0, 0, 0, 0.1);border-radius:2px;pointer-events:none;z-index:100;}#chatgpt-mermaid-_r_317_ .flowchartTitleText{text-anchor:middle;font-size:18px;fill:rgb(13, 13, 13);}#chatgpt-mermaid-_r_317_ rect.text{fill:none;stroke-width:0;}#chatgpt-mermaid-_r_317_ .icon-shape,#chatgpt-mermaid-_r_317_ .image-shape{background-color:rgb(252, 252, 252);text-align:center;}#chatgpt-mermaid-_r_317_ .icon-shape p,#chatgpt-mermaid-_r_317_ .image-shape p{background-color:rgb(252, 252, 252);padding:2px;}#chatgpt-mermaid-_r_317_ .icon-shape .label rect,#chatgpt-mermaid-_r_317_ .image-shape .label rect{opacity:0.5;background-color:rgb(252, 252, 252);fill:rgb(252, 252, 252);}#chatgpt-mermaid-_r_317_ .label-icon{display:inline-block;height:1em;overflow:visible;vertical-align:-0.125em;}#chatgpt-mermaid-_r_317_ .node .label-icon path{fill:currentColor;stroke:revert;stroke-width:revert;}#chatgpt-mermaid-_r_317_ .node .neo-node{stroke:rgb(83, 154, 248);}#chatgpt-mermaid-_r_317_ [data-look="neo"].node rect,#chatgpt-mermaid-_r_317_ [data-look="neo"].cluster rect,#chatgpt-mermaid-_r_317_ [data-look="neo"].node polygon{stroke:url(#chatgpt-mermaid-_r_317_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_317_ [data-look="neo"].swimlane.cluster rect{filter:none;}#chatgpt-mermaid-_r_317_ [data-look="neo"].node path{stroke:url(#chatgpt-mermaid-_r_317_-gradient);stroke-width:1px;}#chatgpt-mermaid-_r_317_ [data-look="neo"].node .outer-path{filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_317_ [data-look="neo"].node .neo-line path{stroke:rgb(83, 154, 248);filter:none;}#chatgpt-mermaid-_r_317_ [data-look="neo"].node circle{stroke:url(#chatgpt-mermaid-_r_317_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_317_ [data-look="neo"].node circle .state-start{fill:#000000;}#chatgpt-mermaid-_r_317_ [data-look="neo"].icon-shape .icon{fill:url(#chatgpt-mermaid-_r_317_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_317_ [data-look="neo"].icon-shape .icon-neo path{stroke:url(#chatgpt-mermaid-_r_317_-gradient);filter:drop-shadow( 1px 2px 2px rgba(185,185,185,1));}#chatgpt-mermaid-_r_317_ .node text{font-size:14px;font-weight:600;letter-spacing:normal;fill:rgb(0, 79, 153);}#chatgpt-mermaid-_r_317_ .edgeLabels text{font-size:13px;font-weight:600;letter-spacing:-0.08px;fill:rgb(0, 79, 153);}#chatgpt-mermaid-_r_317_ .node tspan[font-weight="normal"],#chatgpt-mermaid-_r_317_ .edgeLabels tspan[font-weight="normal"]{font-weight:600;}#chatgpt-mermaid-_r_317_ .edgeLabel .label rect{opacity:1;rx:13px;ry:13px;fill:rgb(245, 250, 255);stroke:rgb(206, 219, 229);stroke-width:1px;}#chatgpt-mermaid-_r_317_ .node rect,#chatgpt-mermaid-_r_317_ .node circle,#chatgpt-mermaid-_r_317_ .node ellipse,#chatgpt-mermaid-_r_317_ .node polygon,#chatgpt-mermaid-_r_317_ .node path{fill:rgb(229, 243, 255);stroke:rgba(0, 0, 0, 0.1);stroke-width:1px;}#chatgpt-mermaid-_r_317_ .node rect{rx:16px;ry:16px;}#chatgpt-mermaid-_r_317_ .mermaid-decision .label-container{fill:rgb(245, 250, 255);stroke:rgb(206, 219, 229);stroke-dasharray:2,2;}#chatgpt-mermaid-_r_317_ .edgePaths .flowchart-link{stroke:rgb(143, 143, 143);stroke-width:1px;stroke-linecap:round;stroke-linejoin:round;}#chatgpt-mermaid-_r_317_ .marker{fill:rgb(143, 143, 143);stroke:rgb(143, 143, 143);}#chatgpt-mermaid-_r_317_ :root{--mermaid-font-family:-apple-system-body,ui-sans-serif,-apple-system,system-ui,"Segoe UI",Helvetica,"Apple Color Emoji",Arial,sans-serif,"Segoe UI Emoji","Segoe UI Symbol";}Kubernetes NodesVictoriaLogs CollectorVictoriaLogs ServerPersistent Storage





VictoriaLogs centralizes logs collected from the Kubernetes environment.
The server is exposed through NodePort 30428.
9. Namespaces
Namespace	Components
argocd	Argo CD
default	WordPress and MySQL
monitoring	Prometheus, Grafana, Alertmanager, Cloudflare Tunnel
logging	VictoriaLogs
metallb-system	MetalLB
kube-system	Kubernetes system components
local-path-storage	Local Path Provisioner

10. Operations
Check nodes:
kubectl get nodes -o wide


Check all Pods:
kubectl get pods -A -o wide


Check Services:
kubectl get services -A


Check storage:
kubectl get pvc -A


Check Argo CD Applications:
kubectl get applications -n argocd


Inspect an Application:
kubectl describe application portfolio-apps -n argocd


Inspect recent events:
kubectl get events -A --sort-by=.metadata.creationTimestamp


11. Troubleshooting Exercise
The repository contains broken-pod.yaml.
It intentionally specifies an invalid Nginx image tag:
image: nginx:typo-version


The file is outside the manifests/ directory and is not
automatically deployed by the main Argo CD Application.

Deploy the test Pod:
kubectl apply -f broken-pod.yaml


Inspect the failure:
kubectl describe pod broken-nginx
kubectl get events --sort-by=.metadata.creationTimestamp


Remove the test Pod:
kubectl delete -f broken-pod.yaml


12. Security Considerations
This is a personal infrastructure and learning environment.
Additional work is required before treating it as production-ready.

Recommended improvements:
- Store credentials securely.
- Never commit actual passwords or API tokens.
- Avoid default passwords.
- Pin container images to specific versions.
- Review external service exposure.
- Configure backups and recovery procedures.
- Apply network policies and workload security controls.
- Review TLS configuration.
Review prometheus-argocd-app.yaml for hardcoded Grafana credentials
before making the repository public.
13. Deployment Status
The following Applications were observed as Synced and Healthy
during the documentation update.
Application	Sync	Health
portfolio-apps	Synced	Healthy
portfolio-monitoring-stack	Synced	Healthy
victoria-logs-collector	Synced	Healthy
victoria-logs-server	Synced	Healthy

These statuses are a snapshot and may change.
14. Future Improvements
- Add GitHub Actions for manifest validation.
- Introduce Kustomize overlays.
- Improve secrets management.
- Add automated backups.
- Expand Grafana dashboards and alert rules.
- Implement network policies.
- Document disaster recovery procedures.
15. Author
David Daisuke

GitHub: david-daisuke

This project demonstrates practical experience with Kubernetes,
GitOps, Argo CD, Helm, networking, monitoring, and centralized logging.
