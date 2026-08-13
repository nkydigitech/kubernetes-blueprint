# Lab 04: Helm — Package Manager for Kubernetes ($0)

> **The App Store for Kubernetes** — Install, upgrade, and manage applications with one command.

---

## 🎯 Objective

Install Helm, add a chart repository, install an application (nginx), customize values, and upgrade/rollback. Understand why Helm is the standard package manager for Kubernetes.

**The Analogy:** Deploying raw YAML is like cooking from scratch every time. Helm is like a meal kit — ingredients pre-measured, recipe included, and you can customize the spice level. One command, full meal.

---

## 💰 Cost Warning

- **$0.00** — All local in kind

---

## 📋 Prerequisites

- Labs 00-03 completed
- kind cluster running

---

## 🔧 Step-by-Step

### Step 1: Install Helm

**Mac:**
```bash
brew install helm
```

**Linux/WSL2:**
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod +x get_helm.sh
./get_helm.sh
```

**Expected Output:**
```
Downloading https://get.helm.sh/helm-v3.15.0-linux-amd64.tar.gz
Helm is now installed in /usr/local/bin/helm
```

```bash
helm version
```

**Expected Output:**
```
version.BuildInfo{Version:"v3.15.0", GitCommit:"...", ...}
```

---

### Step 2: Add a Chart Repository

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

**Expected Output:**
```
"bitnami" has been added to your repositories
```

```bash
helm repo update
```

**Expected Output:**
```
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "bitnami" chart repository
Update Complete. ⎈Happy Helming!⎈
```

---

### Step 3: Search for Charts

```bash
helm search repo nginx
```

**Expected Output:**
```
NAME                 CHART VERSION   APP VERSION   DESCRIPTION
bitnami/nginx        18.x.x          1.27.x        NGINX Open Source...
bitnami/nginx-ingress...
```

---

### Step 4: Install a Chart

```bash
helm install my-nginx bitnami/nginx
```

**Expected Output:**
```
NAME: my-nginx
LAST DEPLOYED: Thu Aug 13 10:00:00 2026
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
...
```

```bash
helm list
```

**Expected Output:**
```
NAME       NAMESPACE  REVISION  UPDATED                STATUS    CHART          APP VERSION
my-nginx   default   1         2026-08-13 10:00:00    deployed  nginx-18.x.x   1.27.x
```

```bash
kubectl get pods
```

**Expected Output:**
```
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-abc123              1/1     Running   0          30s
```

---

### Step 5: Customize with Values

```bash
helm show values bitnami/nginx > my-values.yaml
```

**Expected Output:**
```
(nothing — values written to my-values.yaml)
```

Edit the values:
```bash
cat > custom-values.yaml << 'YAML'
replicaCount: 3
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 250m
    memory: 256Mi
service:
  type: ClusterIP
  port: 80
YAML
```

---

### Step 6: Upgrade with Custom Values

```bash
helm upgrade my-nginx bitnami/nginx -f custom-values.yaml
```

**Expected Output:**
```
Release "my-nginx" has been upgraded. Happy Helming!
NAME: my-nginx
...
REVISION: 2
STATUS: deployed
```

```bash
kubectl get pods
```

**Expected Output:**
```
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-abc123              1/1     Running   0          1s
my-nginx-def456              1/1     Running   0          1s
my-nginx-ghi789              1/1     Running   0          1s
```

Three replicas now (was one before the upgrade).

---

### Step 7: View Revision History

```bash
helm history my-nginx
```

**Expected Output:**
```
REVISION  UPDATED                   STATUS      CHART          APP VERSION  DESCRIPTION
1         2026-08-13 10:00:00       superseded  nginx-18.x.x  1.27.x      Install complete
2         2026-08-13 10:01:00       deployed    nginx-18.x.x  1.27.x      Upgrade complete
```

---

### Step 8: Rollback to a Previous Version

```bash
helm rollback my-nginx 1
```

**Expected Output:**
```
Rollback was a success! Happy Helming!
```

```bash
kubectl get pods
```

**Expected Output:**
```
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-abc123              1/1     Running   0          5s
```

Back to 1 replica (revision 1 settings).

---

## ✅ What You Learned

1. Helm packages Kubernetes applications as "charts"
2. `helm install` deploys an entire application in one command
3. Values files let you customize without editing templates
4. `helm upgrade` updates a release with new values
5. `helm rollback` reverts to a previous revision (like git revert for K8s)
6. Helm tracks revision history automatically

---

## 🧹 Cleanup

```bash
helm uninstall my-nginx
rm my-values.yaml custom-values.yaml
```

**Expected Output:**
```
release "my-nginx" uninstalled
```

---

## 🚀 Production Note

In production:
1. Write your own Helm charts for custom applications
2. Store charts in a chart repository (Azure Container Registry, Harbor, GitHub Pages)
3. Use Helmfile or Argo CD for GitOps-based Helm deployments
4. Pin chart versions in production — never use "latest"
5. Use Helm hooks for pre/post deployment tasks (database migrations, etc.)
