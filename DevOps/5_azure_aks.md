# **AKS (Azure Kubernetes Service)**

# Big Picture (AKS Version)

```
Docker Image
   ↓
Azure Container Registry (ACR)
   ↓
AKS Cluster
   ↓
Deployment → Service → Ingress
   ↓
Public Access
```

---

# AWS vs Azure Mapping (Very Important)

| AWS         | Azure                       |
| ----------- | --------------------------- |
| ECR         | ACR                         |
| EKS         | AKS                         |
| ALB Ingress | NGINX / Application Gateway |
| IAM         | Azure AD / Managed Identity |
| eksctl      | az CLI                      |

---

# STEP 0: Prerequisites

Install & verify:

```bash
az version
kubectl version --client
helm version
docker --version
```

Login to Azure:

```bash
az login
```

Set subscription (if multiple):

```bash
az account set --subscription "<subscription-id>"
```

---

# STEP 1: Create Resource Group

Everything in Azure lives inside a **Resource Group**.

```bash
az group create \
  --name my-aks-rg \
  --location centralindia
```

---

# STEP 2: Create Azure Container Registry (ACR)

```bash
az acr create \
  --resource-group my-aks-rg \
  --name myacr12345 \
  --sku Basic
```

Login to ACR:

```bash
az acr login --name myacr12345
```

ACR login server:

```bash
myacr12345.azurecr.io
```

---

# STEP 3: Push Docker Image to ACR

Assume your local image:

```bash
docker images
# my-app:latest
```

### Tag image:

```bash
docker tag my-app:latest myacr12345.azurecr.io/my-app:latest
```

### Push:

```bash
docker push myacr12345.azurecr.io/my-app:latest
```

---

# STEP 4: Create AKS Cluster

```bash
az aks create \
  --resource-group my-aks-rg \
  --name my-aks-cluster \
  --node-count 2 \
  --node-vm-size Standard_DS2_v2 \
  --enable-managed-identity \
  --generate-ssh-keys
```

⏱️ ~10–15 minutes

---

# STEP 5: Attach ACR to AKS (Very Important)

This allows AKS to pull images from ACR.

```bash
az aks update \
  --name my-aks-cluster \
  --resource-group my-aks-rg \
  --attach-acr myacr12345
```

No secrets needed — uses **Managed Identity** 👍

---

# STEP 6: Get Kubernetes Credentials

```bash
az aks get-credentials \
  --resource-group my-aks-rg \
  --name my-aks-cluster
```

Verify:

```bash
kubectl get nodes
```

---

# STEP 7: Create Kubernetes Deployment

Same as EKS (K8s is cloud-agnostic).

---

## deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: myacr12345.azurecr.io/my-app:latest
        ports:
        - containerPort: 8080
        resources:
          requests:
            cpu: "250m"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
```

Apply:

```bash
kubectl apply -f deployment.yaml
kubectl get pods
```

---

# STEP 8: Expose App Using Service

---

## service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
  type: ClusterIP
```

Apply:

```bash
kubectl apply -f service.yaml
```

---

# STEP 9: Install Ingress Controller (NGINX – Simplest)

AKS does NOT install ingress by default.

---

### Install NGINX Ingress via Helm:

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx
```

Verify:

```bash
kubectl get svc
```

You’ll see:

```
ingress-nginx-controller   LoadBalancer
```

Copy the **EXTERNAL-IP**.

---

# STEP 10: Create Ingress Resource

---

## ingress.yaml

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-service
            port:
              number: 80
```

Apply:

```bash
kubectl apply -f ingress.yaml
```

---

# STEP 11: Access Your Application

Get ingress:

```bash
kubectl get ingress
```

Use:

```
http://<INGRESS-EXTERNAL-IP>/
```

🎉 App is live on AKS!

---

# STEP 12: Enable Auto Scaling (HPA)

AKS already has metrics-server enabled.

```bash
kubectl autoscale deployment my-app \
  --cpu-percent=60 \
  --min=2 \
  --max=10
```

Verify:

```bash
kubectl get hpa
```

---

# STEP 13: Logs & Debugging

```bash
kubectl logs pod-name
kubectl describe pod pod-name
kubectl exec -it pod-name -- sh
```

---

# Production Options in AKS (Important)

### Ingress choices:

* **NGINX** (most common)
* **Azure Application Gateway Ingress Controller (AGIC)**

### TLS:

* cert-manager + Let’s Encrypt
* Azure Key Vault

---

# Final Comparison: EKS vs AKS

| Feature    | EKS      | AKS                 |
| ---------- | -------- | ------------------- |
| Registry   | ECR      | ACR                 |
| Identity   | IAM      | Managed Identity    |
| Ingress    | ALB      | NGINX / App Gateway |
| Setup      | eksctl   | az CLI              |
| Image Pull | IAM Role | ACR Attach          |

---

# Final Mental Model (Same Everywhere)

> **Kubernetes concepts stay the same.
> Only cloud services change.**

---