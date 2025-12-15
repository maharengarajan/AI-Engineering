# **AWS EKS (Elastic Kubernetes Service)**

I’ll keep it **practical, production-oriented**, and **clear**, with **commands + explanation** at every step.

---

# Big Picture (Before Steps)

Your flow will be:

```
Docker Image
   ↓
Amazon ECR (Image Registry)
   ↓
EKS Cluster
   ↓
Deployment → Service → Ingress
   ↓
Public Access
```

---

# STEP 0: Prerequisites (One-Time Setup)

Make sure you have:

✅ AWS account
✅ AWS CLI installed
✅ kubectl installed
✅ eksctl installed
✅ Docker installed

Verify:

```bash
aws --version
kubectl version --client
eksctl version
docker --version
```

---

# STEP 1: Configure AWS Credentials

Login to AWS:

```bash
aws configure
```

Enter:

* AWS Access Key
* Secret Key
* Region (example: `ap-south-1`)
* Output format: `json`

Verify:

```bash
aws sts get-caller-identity
```

---

# STEP 2: Create an EKS Cluster (Managed Kubernetes)

We’ll use **eksctl** (simplest & recommended).

```bash
eksctl create cluster \
  --name my-eks-cluster \
  --region ap-south-1 \
  --nodegroup-name standard-nodes \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 3 \
  --managed
```

⏱️ Takes ~15–20 minutes.

### What this creates:

* EKS control plane
* Worker nodes (EC2)
* Networking (VPC, subnets)
* IAM roles

---

### Verify cluster:

```bash
kubectl get nodes
```

If you see nodes → cluster is ready ✅

---

# STEP 3: Push Your Docker Image to Amazon ECR

EKS pulls images **from ECR**, not your local system.

---

## 3.1 Create ECR Repository

```bash
aws ecr create-repository \
  --repository-name my-app \
  --region ap-south-1
```

Output gives:

```
<account-id>.dkr.ecr.ap-south-1.amazonaws.com/my-app
```

---

## 3.2 Login Docker to ECR

```bash
aws ecr get-login-password --region ap-south-1 \
| docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-south-1.amazonaws.com
```

---

## 3.3 Tag Your Image

Assume your local image is:

```bash
docker images
# my-app:latest
```

Tag it:

```bash
docker tag my-app:latest \
<account-id>.dkr.ecr.ap-south-1.amazonaws.com/my-app:latest
```

---

## 3.4 Push Image to ECR

```bash
docker push <account-id>.dkr.ecr.ap-south-1.amazonaws.com/my-app:latest
```

✅ Image is now available for EKS

---

# STEP 4: Create Kubernetes Deployment

This tells Kubernetes **how to run your app**.

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
        image: <account-id>.dkr.ecr.ap-south-1.amazonaws.com/my-app:latest
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

Apply it:

```bash
kubectl apply -f deployment.yaml
kubectl get pods
```

---

# STEP 5: Expose App Using a Service

Pods are internal → need Service.

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

# STEP 6: Install Ingress Controller (AWS ALB)

AWS EKS uses **ALB Ingress Controller**.

---

## 6.1 Associate IAM OIDC Provider

```bash
eksctl utils associate-iam-oidc-provider \
  --region ap-south-1 \
  --cluster my-eks-cluster \
  --approve
```

---

## 6.2 Install AWS Load Balancer Controller

```bash
eksctl create iamserviceaccount \
  --cluster=my-eks-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=arn:aws:iam::aws:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

Install controller via Helm:

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=my-eks-cluster
```

---

# STEP 7: Create Ingress (Public Access)

---

## ingress.yaml

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
spec:
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

### Get Public URL:

```bash
kubectl get ingress
```

You’ll see:

```
ADDRESS: xxxx.ap-south-1.elb.amazonaws.com
```

🎉 Your app is live on AWS EKS!

---

# STEP 8: Enable Auto Scaling (Optional but Recommended)

## Horizontal Pod Autoscaler

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

# STEP 9: Logs & Debugging

```bash
kubectl logs pod-name
kubectl describe pod pod-name
kubectl exec -it pod-name -- sh
```

---

# STEP 10: Production Best Practices

✅ Use **Helm** for deployments
✅ Use **Ingress + TLS**
✅ Enable **HPA**
✅ Separate **dev / staging / prod**
✅ Monitor using **Prometheus + Grafana**

---

# Final Mental Model (VERY IMPORTANT)

```
ECR → stores images
EKS → runs containers
Deployment → manages pods
Service → stable networking
Ingress → public access
HPA → auto scaling
```

---
