# ☸️ Kubernetes Blueprint: Zero to Hero

![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.30+-326CE5?logo=kubernetes)
![Minikube](https://img.shields.io/badge/Minikube-Local_Cluster-4A90D9?logo=minikube)
![Helm](https://img.shields.io/badge/Helm-v3-0F1689?logo=helm)
![Beginners](https://img.shields.io/badge/Made%20for-Beginners-0a0e1a?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue)

> **From zero to deploying on K8s — one lab at a time.**

**Built by [Nkechi Anna Ahanonye](https://www.linkedin.com/in/nkechiahanonye) — Cloud & DevOps Engineer | I turn manual, 3 AM-breaking deployments into 1-min automated pipelines with AWS + Ansible + Terraform | Featured: 15-Module Ansible Lab with real terminal**

For DevOps students who need relatable, hands-on examples — not textbook theory.

A static, beginner-to-hero Kubernetes learning platform designed for GitHub Pages. No build system required — just open `docs/index.html` in your browser.

---

## What's Inside

- **Kubernetes Fundamentals** — what K8s is, why it exists, and how it solves real problems
- **Architecture** — control plane, nodes, pods, services explained with analogies
- **Workloads** — Deployments, ReplicaSets, StatefulSets, DaemonSets, Jobs
- **Networking** — Services (ClusterIP, NodePort, LoadBalancer), Ingress, DNS
- **Storage** — PersistentVolumes, PersistentVolumeClaims, StorageClasses
- **Configuration** — ConfigMaps, Secrets, environment injection
- **Security** — RBAC, ServiceAccounts, NetworkPolicies, Pod Security
- **Helm** — package management, charts, and templating
- **Monitoring & Scaling** — HPA, VPA, resource limits, health checks
- **Hands-on Labs** — progressive exercises with Minikube
- **Capstone Project** — deploy a real multi-tier app on K8s
- **Career Roadmap** — from K8s beginner to production engineer

## Run Locally

### Option 1: Open the file
Open `docs/index.html` in any browser.

### Option 2: Local web server
```bash
python -m http.server 8000
```
Then visit `http://localhost:8000/docs/`.

## GitHub Pages

1. Go to **Settings → Pages**
2. Select **Deploy from a branch**
3. Choose `main` and `/docs`
4. Save and wait for GitHub Pages to publish

🔗 **Live Site:** `https://nkydigitech.github.io/kubernetes-blueprint/`

## Suggested Learning Path

Start Here → Fundamentals → Architecture → Pods → Deployments → Services → ConfigMaps & Secrets → Storage → Ingress → RBAC → Helm → Labs → Capstone → Career

## Prerequisites

- Basic understanding of containers and Docker
- [Minikube](https://minikube.sigs.k8s.io/) installed (for hands-on labs)
- A code editor (we recommend [VS Code](https://code.visualstudio.com/))
- A curious mind 🧠

## Part of the Blueprint Series

| # | Blueprint | Category | Status |
|---|-----------|----------|--------|
| 1 | [ansible-guide](https://github.com/nkydigitech/ansible-guide) | Automation | ✅ Live |
| 2 | [terraform-blueprint](https://github.com/nkydigitech/terraform-blueprint) | IaC | ✅ Live |
| 3 | [aws-blueprint](https://github.com/nkydigitech/aws-blueprint) | Cloud | ✅ Live |
| 4 | [azure-blueprint](https://github.com/nkydigitech/azure-blueprint) | Cloud | ✅ Live |
| 5 | **kubernetes-blueprint** | Orchestration | ✅ Live |
| 6 | [linux-blueprint](https://github.com/nkydigitech/linux-blueprint) | Fundamentals | 🚧 Coming Soon |
| 7 | [github-blueprint](https://github.com/nkydigitech/github-blueprint) | Version Control | 🚧 Coming Soon |
| 8 | [docker-blueprint](https://github.com/nkydigitech/docker-blueprint) | Containers | 🚧 Coming Soon |
| 9 | [bash-scripting-blueprint](https://github.com/nkydigitech/bash-scripting-blueprint) | Scripting | 🚧 Coming Soon |
| 10 | [sdlc-blueprint](https://github.com/nkydigitech/sdlc-blueprint) | Methodology | 🚧 Coming Soon |
| 11 | [cicd-blueprint](https://github.com/nkydigitech/cicd-blueprint) | CI/CD | 🚧 Coming Soon |
| 12 | [jenkins-blueprint](https://github.com/nkydigitech/jenkins-blueprint) | CI/CD | 🚧 Coming Soon |
| 13 | [azure-devops-blueprint](https://github.com/nkydigitech/azure-devops-blueprint) | DevOps Platform | 🚧 Coming Soon |
| 14 | [prometheus-blueprint](https://github.com/nkydigitech/prometheus-blueprint) | Monitoring | 🚧 Coming Soon |
| 15 | [grafana-blueprint](https://github.com/nkydigitech/grafana-blueprint) | Visualization | 🚧 Coming Soon |
| 16 | [openshift-blueprint](https://github.com/nkydigitech/openshift-blueprint) | Enterprise K8s | 🚧 Coming Soon |
| 17 | [cybersecurity-blueprint](https://github.com/nkydigitech/cybersecurity-blueprint) | DevSecOps | 🚧 Coming Soon |

## Hands-On Labs (All $0 Cost with kind)

| Lab | Topic | Cost |
|-----|-------|------|
| [Lab 00](labs/00-setup/) | Setup: kind Cluster + kubectl | $0 |
| [Lab 01](labs/01-pods-deployments/) | Pods and Deployments — Run and Scale | $0 |
| [Lab 02](labs/02-services-networking/) | Services and Networking — Expose Your App | $0 |
| [Lab 03](labs/03-configmaps-secrets/) | ConfigMaps and Secrets — Configure Apps | $0 |
| [Lab 04](labs/04-helm/) | Helm — Package Manager for Kubernetes | $0 |
| [Lab 05](labs/05-capstone/) | Capstone: Multi-Tier App with Helm and Ingress | $0 |

All labs use **kind** (Kubernetes in Docker) — a real K8s cluster running locally at zero cost.

## Connect

- **LinkedIn:** [Nkechi Ahanonye](https://www.linkedin.com/in/nkechiahanonye)
- **X (Twitter):** [@NAhanonye](https://www.x.com/NAhanonye)
- **Facebook:** [NkyDigitech](https://web.facebook.com/nkydigitech)
- **Portfolio:** [nkydigitech.github.io/nky-portfolio](https://nkydigitech.github.io/nky-portfolio/)

---

*Built with ❤️ for the DevOps community. Especially for African engineers who deserve accessible, relatable learning resources.*
