# Lab 03: ConfigMaps and Secrets — Configure Your Apps ($0)

> **The Settings Panel** — Separate configuration from code. Change settings without rebuilding images.

---

## 🎯 Objective

Create ConfigMaps for app configuration and Secrets for sensitive data. Mount them into Pods as environment variables and files.

**The Analogy:** ConfigMaps are like the settings menu on your phone — you change the language, brightness, and wallpaper without reinstalling the OS. Secrets are the PIN code — same idea, but hidden from plain sight.

---

## 💰 Cost Warning

- **$0.00** — All local in kind

---

## 📋 Prerequisites

- Labs 00-02 completed
- kind cluster running

---

## 🔧 Step-by-Step

### Step 1: Create a ConfigMap

```bash
kubectl create configmap app-config \
  --from-literal=APP_ENV=development \
  --from-literal=LOG_LEVEL=debug \
  --from-literal=MAX_CONNECTIONS=100
```

**Expected Output:**
```
configmap/app-config created
```

```bash
kubectl get configmaps
```

**Expected Output:**
```
NAME        DATA   AGE
app-config  3      5s
```

```bash
kubectl describe configmap app-config
```

**Expected Output:**
```
Name:         app-config
Data
====
APP_ENV:       development
LOG_LEVEL:     debug
MAX_CONNECTIONS: 100
```

---

### Step 2: Create a Secret

```bash
kubectl create secret generic app-secrets \
  --from-literal=DB_PASSWORD=supersecret123 \
  --from-literal=API_KEY=abc-def-ghi-jkl
```

**Expected Output:**
```
secret/app-secrets created
```

```bash
kubectl get secrets
```

**Expected Output:**
```
NAME           TYPE     DATA   AGE
app-secrets    Opaque   2      5s
```

```bash
kubectl describe secret app-secrets
```

**Expected Output:**
```
Name:         app-secrets
Data
====
API_KEY:       12 bytes
DB_PASSWORD:   14 bytes
```

> Note: Secrets show byte sizes, NOT values. This is a security feature.

---

### Step 3: Decode a Secret (When You Need To)

```bash
kubectl get secret app-secrets -o jsonpath='{.data.DB_PASSWORD}' | base64 --decode
```

**Expected Output:**
```
supersecret123
```

> ⚠️ Secrets are base64-encoded, NOT encrypted. Anyone with cluster access can decode them. In production, use a secrets manager (Vault, Sealed Secrets, External Secrets).

---

### Step 4: Mount ConfigMap and Secret as Environment Variables

```bash
cat > app-pod.yaml << 'YAML'
apiVersion: v1
kind: Pod
metadata:
  name: config-demo
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "echo ENV=$APP_ENV LOG=$LOG_LEVEL PASS=$DB_PASSWORD && sleep 3600"]
    env:
    - name: APP_ENV
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_ENV
    - name: LOG_LEVEL
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: LOG_LEVEL
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secrets
          key: DB_PASSWORD
  restartPolicy: Never
YAML

kubectl apply -f app-pod.yaml
```

**Expected Output:**
```
pod/config-demo created
```

---

### Step 5: Check the Pod Logs

```bash
kubectl logs config-demo
```

**Expected Output:**
```
ENV=development LOG=debug PASS=supersecret123
```

Both ConfigMap and Secret values were injected as environment variables.

---

### Step 6: Mount ConfigMap as a File

```bash
cat > file-pod.yaml << 'YAML'
apiVersion: v1
kind: Pod
metadata:
  name: file-demo
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "cat /etc/config/APP_ENV && sleep 3600"]
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
  restartPolicy: Never
YAML

kubectl apply -f file-pod.yaml
```

**Expected Output:**
```
pod/file-demo created
```

```bash
kubectl logs file-demo
```

**Expected Output:**
```
development
```

The ConfigMap was mounted as files in /etc/config/. Each key becomes a file.

---

## ✅ What You Learned

1. ConfigMaps store non-sensitive configuration as key-value pairs
2. Secrets store sensitive data (base64-encoded, not encrypted)
3. Both can be injected as environment variables OR mounted as files
4. Secrets show byte sizes, not values (security by obscurity)
5. Decoding a secret: `kubectl get secret ... -o jsonpath | base64 --decode`

---

## 🧹 Cleanup

```bash
kubectl delete pod config-demo file-demo
kubectl delete configmap app-config
kubectl delete secret app-secrets
rm app-pod.yaml file-pod.yaml
```

---

## 🚀 Production Note

In production:
1. Use External Secrets with AWS Secrets Manager or Azure Key Vault
2. Use Sealed Secrets to encrypt secrets in Git repos
3. NEVER store raw secrets in Git — use a secrets manager
4. Use RBAC to restrict who can read secrets
5. Enable etcd encryption at rest for an extra layer of security
