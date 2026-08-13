# Lab 02: Services and Networking — Expose Your App ($0)

> **The Front Desk** — Services make your Pods reachable. Without a Service, a Pod is like a house with no address.

---

## 🎯 Objective

Create a Deployment, expose it with ClusterIP, NodePort, and LoadBalancer Services. Understand how Kubernetes networking routes traffic to Pods.

**The Analogy:** Pods are houses in an estate. Services are the street addresses and the estate gate. Without addresses, nobody can find your house. The LoadBalancer is the main gate — it routes visitors to the right house.

---

## 💰 Cost Warning

- **$0.00** — All local in kind

---

## 📋 Prerequisites

- Lab 01 completed
- kind cluster running

---

## 🔧 Step-by-Step

### Step 1: Create a Deployment

```bash
kubectl create deployment web-app --image=nginx --replicas=3
```

**Expected Output:**
```
deployment.apps/web-app created
```

---

### Step 2: Expose as ClusterIP (Internal Only)

```bash
kubectl expose deployment web-app --port=80 --name=web-clusterip
```

**Expected Output:**
```
service/web-clusterip exposed
```

```bash
kubectl get services
```

**Expected Output:**
```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP   5m
web-clusterip   ClusterIP   10.96.123.45    <none>        80/TCP    5s
```

ClusterIP is only reachable from INSIDE the cluster. Test it:
```bash
kubectl run test-pod --image=busybox --rm -it -- curl http://web-clusterip
```

**Expected Output:**
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

---

### Step 3: Expose as NodePort (Reachable from Host)

```bash
kubectl expose deployment web-app --port=80 --type=NodePort --name=web-nodeport
```

**Expected Output:**
```
service/web-nodeport exposed
```

```bash
kubectl get services
```

**Expected Output:**
```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
web-clusterip   ClusterIP   10.96.123.45    <none>        80/TCP           2m
web-nodeport    NodePort    10.96.234.56    <none>        80:31234/TCP     5s
```

The port `31234` is assigned on the node. Test it:
```bash
# Get the kind node's port mapping
curl http://localhost:31234
```

> ⚠️ In kind, NodePort requires port mapping. If curl fails, use `kubectl port-forward`:
```bash
kubectl port-forward service/web-nodeport 8080:80 &
curl http://localhost:8080
```

**Expected Output:**
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

---

### Step 4: Expose as LoadBalancer

```bash
kubectl expose deployment web-app --port=80 --type=LoadBalancer --name=web-lb
```

**Expected Output:**
```
service/web-lb exposed
```

```bash
kubectl get services
```

**Expected Output:**
```
NAME            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
web-clusterip   ClusterIP     10.96.123.45    <none>        80/TCP         5m
web-lb          LoadBalancer   10.96.345.67    <pending>     80:32345/TCP   5s
web-nodeport    NodePort       10.96.234.56    <none>        80:31234/TCP  3m
```

> ⚠️ EXTERNAL-IP shows `<pending>` because kind does not have a real cloud load balancer. In production (EKS, AKS, GKE), this would show a real IP.

---

### Step 5: Use Port-Forward for Local Access

```bash
kubectl port-forward service/web-clusterip 8080:80
```

**Expected Output:**
```
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

Open browser: http://localhost:8080

You should see the nginx welcome page.

---

### Step 6: See Which Pods a Service Routes To

```bash
kubectl get endpoints web-clusterip
```

**Expected Output:**
```
NAME            ENDPOINTS                              AGE
web-clusterip   10.244.0.5:80,10.244.0.6:80,10.244.0.7:80   5m
```

Three endpoints = three pods. The Service load-balances traffic across all three.

---

## ✅ What You Learned

1. ClusterIP = internal-only, reachable from other pods
2. NodePort = opens a port on the node (30000-32767)
3. LoadBalancer = creates a cloud load balancer (shows pending in kind)
4. Services automatically track pods — if a pod dies, the endpoint is removed
5. Port-forward is the easiest way to access a service locally

---

## 🧹 Cleanup

```bash
kubectl delete service web-clusterip web-nodeport web-lb
kubectl delete deployment web-app
```

**Expected Output:**
```
service "web-clusterip" deleted
service "web-nodeport" deleted
service "web-lb" deleted
deployment.apps "web-app" deleted
```

---

## 🚀 Production Note

In production:
1. Use Ingress controllers (nginx-ingress, traefik) for HTTP routing
2. LoadBalancer services create real cloud load balancers (costs money)
3. Use cert-manager for automatic TLS certificates
4. Configure NetworkPolicies to restrict pod-to-pod communication
5. Use ExternalDNS to auto-create DNS records for your services
