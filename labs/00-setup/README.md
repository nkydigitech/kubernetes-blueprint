# Lab 00: Setup — kind (Kubernetes in Docker) ($0)

> **Your Personal Kubernetes Cluster** — A real K8s cluster running on your laptop, for free.

---

## 🎯 Objective

Install kind (Kubernetes in Docker), create a local cluster, and verify it works. At the end, you will have a fully functional Kubernetes cluster running on your machine at zero cost.

**The Analogy:** kind is like a mini power station in your house. It generates real electricity (Kubernetes), powers real appliances (containers), but you are not connected to the national grid (cloud K8s). Same technology, zero bill.

---

## 💰 Cost Warning

- **$0.00** — Everything runs locally in Docker
- No cloud account needed
- No credit card needed

---

## 📋 Prerequisites

- Docker running on your machine
- kubectl will be installed in this lab
- Windows: WSL2 or Docker Desktop with Kubernetes enabled

---

## 🔧 Step-by-Step

### Step 1: Install kind

**Mac:**
```bash
brew install kind
```

**Expected Output:**
```
==> Downloading https://ghcr.io/v2/homebrew/core/kind/manifests...
==> Pouring kind--0.23.0.bottle.tar.gz
🍺  kind was successfully installed!
```

**Linux/WSL2:**
```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

**Expected Output:**
```
(nothing — kind binary is installed)
```

---

### Step 2: Install kubectl

**Mac:**
```bash
brew install kubectl
```

**Linux/WSL2:**
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

**Expected Output:**
```
(nothing — kubectl binary is installed)
```

---

### Step 3: Verify Both Tools

```bash
kind version
kubectl version --client
```

**Expected Output:**
```
kind v0.23.0 go1.22.0 linux/amd64
Client Version: v1.30.0
```

---

### Step 4: Create Your First Cluster

```bash
kind create cluster --name blueprint
```

**Expected Output:**
```
Creating cluster "blueprint" ...
 • Ensuring node image (kindest/node:v1.30.0) 🖼
 • Preparing nodes 📦
 • Writing configuration 📜
 • Starting control-plane 🕹️
 • Installing CNI 🔌
 • Installing StorageClass 💾
 • Waiting ≤ 2m0s for control-plane = Ready ⏳
 • Ready! ✅
Set kubectl context to "kind-blueprint"
```

---

### Step 5: Verify the Cluster

```bash
kubectl cluster-info
```

**Expected Output:**
```
Kubernetes control plane is running at https://127.0.0.1:32768
CoreDNS is running at https://127.0.0.1:32768/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

```bash
kubectl get nodes
```

**Expected Output:**
```
NAME                        STATUS   ROLES           AGE   VERSION
blueprint-control-plane     Ready    control-plane   30s   v1.30.0
```

```bash
kubectl get pods -A
```

**Expected Output:**
```
NAMESPACE            NAME                                        READY   STATUS    RESTARTS   AGE
kube-system          coredns-abc-xyz                             1/1     Running   0          30s
kube-system          etcd-blueprint-control-plane                1/1     Running   0          30s
kube-system          kindnet-abc                                 1/1     Running   0          30s
kube-system          kube-apiserver-blueprint-control-plane      1/1     Running   0          30s
kube-system          kube-controller-manager-blueprint-control   1/1     Running   0          30s
kube-system          kube-proxy-abc                              1/1     Running   0          30s
kube-system          kube-scheduler-blueprint-control-plane      1/1     Running   0          30s
```

All pods should show `Running` status. Your cluster is alive!

---

### Step 6: Run Your First Pod

```bash
kubectl run nginx --image=nginx --port=80
```

**Expected Output:**
```
pod/nginx created
```

```bash
kubectl get pods
```

**Expected Output:**
```
NAME    READY   STATUS              RESTARTS   AGE
nginx   0/1     ContainerCreating   0          3s
```

Wait a few seconds, check again:
```bash
kubectl get pods
```

**Expected Output:**
```
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          10s
```

---

## ✅ What You Learned

1. kind creates a real Kubernetes cluster inside Docker — zero cost
2. kubectl is the main tool for interacting with K8s clusters
3. A fresh cluster has system pods in the kube-system namespace
4. `kubectl run` creates a pod (the smallest K8s unit)
5. Pods go through phases: Pending → ContainerCreating → Running

---

## 🧹 Cleanup

```bash
# Delete the pod
kubectl delete pod nginx

# Delete the entire cluster
kind delete cluster --name blueprint
```

**Expected Output:**
```
pod "nginx" deleted
Deleting cluster "blueprint" ...
Deleted nodes: ["blueprint-control-plane"]
```

---

## 🚀 Production Note

In production:
1. Use managed K8s (EKS, AKS, GKE) instead of kind
2. kind is for development and testing only — not production workloads
3. In cloud K8s, the control plane is managed for you (you don't run etcd)
4. Use CI/CD pipelines to deploy, never manual kubectl in production
