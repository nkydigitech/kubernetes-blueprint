# Lab 01: Pods and Deployments — Run and Scale Apps ($0)

> **From One Pod to Many** — Learn the building blocks of Kubernetes: Pods, Deployments, and scaling.

---

## 🎯 Objective

Create Deployments, scale them up and down, update the image, and roll back. Understand the difference between a Pod and a Deployment.

**The Analogy:** A Pod is one worker. A Deployment is the HR department that hires, fires, and manages workers. If a worker quits (pod dies), HR (Deployment) immediately hires a replacement. You always have the right number of workers.

---

## 💰 Cost Warning

- **$0.00** — All local in kind

---

## 📋 Prerequisites

- Lab 00 completed (kind cluster running)
- Your cluster should be up: `kind create cluster --name blueprint`

---

## 🔧 Step-by-Step

### Step 1: Create a Deployment

```bash
kubectl create deployment nginx-app --image=nginx --replicas=3
```

**Expected Output:**
```
deployment.apps/nginx-app created
```

---

### Step 2: Check the Deployment

```bash
kubectl get deployments
```

**Expected Output:**
```
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
nginx-app   3/3     3            3           10s
```

```bash
kubectl get pods
```

**Expected Output:**
```
NAME                         READY   STATUS    RESTARTS   AGE
nginx-app-abc123-xyz789      1/1     Running   0          15s
nginx-app-abc123-def456      1/1     Running   0          15s
nginx-app-abc123-ghi012      1/1     Running   0          15s
```

You have 3 pods running. The Deployment ensures this number stays at 3.

---

### Step 3: Scale Up

```bash
kubectl scale deployment nginx-app --replicas=5
```

**Expected Output:**
```
deployment.apps/nginx-app scaled
```

```bash
kubectl get pods
```

**Expected Output:**
```
NAME                         READY   STATUS    RESTARTS   AGE
nginx-app-abc123-xyz789      1/1     Running   0          30s
nginx-app-abc123-def456      1/1     Running   0          30s
nginx-app-abc123-ghi012      1/1     Running   0          30s
nginx-app-abc123-jkl345      1/1     Running   0          3s
nginx-app-abc123-mno678      1/1     Running   0          3s
```

Two new pods were created automatically.

---

### Step 4: Scale Down

```bash
kubectl scale deployment nginx-app --replicas=2
```

**Expected Output:**
```
deployment.apps/nginx-app scaled
```

```bash
kubectl get pods
```

**Expected Output:**
```
NAME                         READY   STATUS    RESTARTS   AGE
nginx-app-abc123-xyz789      1/1     Running   0          45s
nginx-app-abc123-def456      1/1     Running   0          45s
```

Three pods were terminated. The Deployment keeps only the desired count.

---

### Step 5: Delete a Pod and Watch Self-Healing

```bash
# Get a pod name
POD_NAME=$(kubectl get pods -o name | head -1)
echo $POD_NAME
```

**Expected Output:**
```
pod/nginx-app-abc123-xyz789
```

```bash
# Delete it
kubectl delete $POD_NAME
```

**Expected Output:**
```
pod "nginx-app-abc123-xyz789" deleted
```

```bash
# Immediately check — a new pod is already being created
kubectl get pods
```

**Expected Output:**
```
NAME                         READY   STATUS              RESTARTS   AGE
nginx-app-abc123-def456      1/1     Running             0          1m
nginx-app-abc123-pqr901      0/1     ContainerCreating   0          2s
```

The Deployment detected the dead pod and immediately started a replacement. This is self-healing.

---

### Step 6: Update the Image (Rolling Update)

```bash
kubectl set image deployment/nginx-app nginx=nginx:1.25
```

**Expected Output:**
```
deployment.apps/nginx-app image updated
```

```bash
kubectl rollout status deployment/nginx-app
```

**Expected Output:**
```
Waiting for deployment "nginx-app" rollout to finish: 1 out of 2 new replicas have been updated...
deployment "nginx-app" successfully rolled out
```

---

### Step 7: Roll Back (Undo)

```bash
kubectl rollout undo deployment/nginx-app
```

**Expected Output:**
```
deployment.apps/nginx-app rolled back
```

```bash
kubectl rollout status deployment/nginx-app
```

**Expected Output:**
```
deployment "nginx-app" successfully rolled out
```

---

## ✅ What You Learned

1. A Deployment manages Pods and ensures a desired count is always running
2. Scaling changes the replica count instantly
3. If a Pod dies, the Deployment automatically creates a replacement (self-healing)
4. Rolling updates replace pods one at a time (zero downtime)
5. Rollback undoes a bad update in seconds

---

## 🧹 Cleanup

```bash
kubectl delete deployment nginx-app
```

**Expected Output:**
```
deployment.apps "nginx-app" deleted
```

---

## 🚀 Production Note

In production:
1. Use YAML manifests (not imperative commands) so infrastructure is version-controlled
2. Set resource requests and limits on every container
3. Configure Horizontal Pod Autoscaler (HPA) for automatic scaling based on CPU/memory
4. Use readiness and liveness probes for health checks
5. Configure Pod Disruption Budgets to prevent too many pods dying during updates
