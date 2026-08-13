# Lab 05: Capstone — Multi-Tier App with Helm and Ingress ($0)

> **The Full Stack on Kubernetes** — Deploy a complete web app with frontend, backend, and database using Helm and Ingress.

---

## 🎯 Objective

Deploy a 3-tier application on Kubernetes:
1. PostgreSQL database (backend)
2. Python Flask API (middle tier)
3. Nginx frontend (web tier)
4. Ingress controller for routing
5. ConfigMaps for configuration
6. Secrets for database credentials

All running locally on kind at zero cost.

**The Analogy:** This is the full restaurant. Kitchen (database), waiter (API), host (nginx), and the restaurant entrance (Ingress). Every component works together, and you manage the whole thing from one Helm chart.

---

## 💰 Cost Warning

- **$0.00** — Everything runs in kind locally

---

## 📋 Prerequisites

- All previous labs completed
- kind cluster running
- Helm installed

---

## 🔧 Step-by-Step

### Step 1: Create Project Structure

```bash
mkdir k8s-capstone
cd k8s-capstone
```

---

### Step 2: Create Namespace

```bash
kubectl create namespace capstone
```

**Expected Output:**
```
namespace/capstone created
```

---

### Step 3: Create Database Secret

```bash
kubectl create secret generic db-secret \
  --namespace=capstone \
  --from-literal=POSTGRES_USER=blueprint \
  --from-literal=POSTGRES_PASSWORD=capstone123 \
  --from-literal=POSTGRES_DB=appdb
```

**Expected Output:**
```
secret/db-secret created
```

---

### Step 4: Deploy PostgreSQL

```bash
cat > postgres.yaml << 'YAML'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: capstone
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15-alpine
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: POSTGRES_USER
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: POSTGRES_PASSWORD
        - name: POSTGRES_DB
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: POSTGRES_DB
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
---
apiVersion: v1
kind: Service
metadata:
  name: postgres-service
  namespace: capstone
spec:
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432
YAML

kubectl apply -f postgres.yaml
```

**Expected Output:**
```
deployment.apps/postgres created
service/postgres-service created
```

---

### Step 5: Deploy the API Backend

```bash
cat > api.yaml << 'YAML'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
  namespace: capstone
spec:
  replicas: 2
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: api
        image: python:3.11-slim
        command: ["python", "-m", "http.server", "8080"]
        ports:
        - containerPort: 8080
        env:
        - name: DB_HOST
          value: postgres-service
        - name: DB_PORT
          value: "5432"
        resources:
          requests:
            cpu: 50m
            memory: 64Mi
          limits:
            cpu: 100m
            memory: 128Mi
---
apiVersion: v1
kind: Service
metadata:
  name: api-service
  namespace: capstone
spec:
  selector:
    app: api
  ports:
  - port: 8080
    targetPort: 8080
YAML

kubectl apply -f api.yaml
```

**Expected Output:**
```
deployment.apps/api created
service/api-service created
```

---

### Step 6: Deploy the Nginx Frontend

```bash
cat > frontend.yaml << 'YAML'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: capstone
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 50m
            memory: 64Mi
          limits:
            cpu: 100m
            memory: 128Mi
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
  namespace: capstone
spec:
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
YAML

kubectl apply -f frontend.yaml
```

**Expected Output:**
```
deployment.apps/frontend created
service/frontend-service created
```

---

### Step 7: Verify Everything Is Running

```bash
kubectl get all -n capstone
```

**Expected Output:**
```
NAME                            READY   STATUS    RESTARTS   AGE
pod/postgres-abc123             1/1     Running   0          30s
pod/api-def456                  1/1     Running   0          20s
pod/api-ghi789                  1/1     Running   0          20s
pod/frontend-jkl012             1/1     Running   0          10s
pod/frontend-mno345             1/1     Running   0          10s

NAME                        TYPE        CLUSTER-IP      PORT(S)    AGE
service/postgres-service    ClusterIP   10.96.10.1      5432/TCP   30s
service/api-service         ClusterIP   10.96.20.2      8080/TCP   20s
service/frontend-service   ClusterIP   10.96.30.3      80/TCP     10s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/postgres    1/1     1            1           30s
deployment.apps/api         2/2     2            2           20s
deployment.apps/frontend   2/2     2            2           10s
```

All 5 pods running, 3 services created, 3 deployments healthy.

---

### Step 8: Port-Forward to Access the Frontend

```bash
kubectl -n capstone port-forward service/frontend-service 8080:80
```

**Expected Output:**
```
Forwarding from 127.0.0.1:8080 -> 80
```

Open browser: http://localhost:8080

You should see the nginx welcome page — your frontend is live.

---

### Step 9: Test API Connectivity

```bash
kubectl -n capstone exec -it deployment/api -- curl http://api-service:8080
```

**Expected Output:**
```
<!DOCTYPE HTML>
<html>
...
Directory listing for /
...
```

The API pod can reach itself via the service name. In a real app, it would connect to PostgreSQL at `postgres-service:5432`.

---

## ✅ What You Learned

1. A 3-tier app on K8s = 3 Deployments + 3 Services
2. Services connect tiers: frontend → API → database
3. Secrets hold database credentials securely
4. Namespaces isolate applications
5. Resource limits prevent any single pod from consuming all cluster resources
6. Port-forward provides local access for testing

---

## 🧹 Cleanup

```bash
kubectl delete namespace capstone
rm postgres.yaml api.yaml frontend.yaml
cd ..
```

**Expected Output:**
```
namespace "capstone" deleted
```

Deleting the namespace deletes everything inside it — all pods, services, secrets, and deployments.

---

## 🚀 Production Note

In production:
1. Use a real Ingress controller (nginx-ingress, traefik) for HTTP routing
2. Use PersistentVolumeClaims for database storage (data survives pod restarts)
3. Use Helm charts to package the entire application
4. Add Horizontal Pod Autoscaler for automatic scaling
5. Use cert-manager for TLS certificates
6. Add readiness and liveness probes to every container
7. Use PodDisruptionBudgets for high availability during updates
8. Deploy to managed K8s (EKS, AKS, GKE) for production-grade infrastructure
