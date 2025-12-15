# Step 0: Why Kubernetes Exists (Very Important)

Before Kubernetes, teams used:

* **VMs** → heavy, slow to scale
* **Docker containers** → lightweight, but managing **100s/1000s of containers** manually is painful

### Real problems:

* How to **deploy** containers?
* How to **restart** if a container crashes?
* How to **scale** up/down?
* How to **expose** apps to users?
* How to **update** without downtime?

👉 **Kubernetes is a container orchestration system** that solves all of this **automatically**.

> **In one line:**
> Kubernetes = *An operating system for containers*

---

# Step 1: Prerequisites (Mental Model)

You should already know or quickly revise:

* **Linux basics**
* **Docker (must)**

  * Image
  * Container
  * Dockerfile
  * docker run / build

👉 Kubernetes **does NOT replace Docker**
👉 Kubernetes **manages Docker containers**

---

# Step 2: What Kubernetes Actually Is

Kubernetes is:

* An **open-source platform**
* Runs on a **cluster of machines**
* Manages **containerized applications**

### Cluster =

* **Master (Control Plane)** → brain
* **Worker Nodes** → run containers

---

# Step 3: Kubernetes High-Level Architecture

### Kubernetes Cluster Components

```
+---------------------+
|   Control Plane     |
|---------------------|
| API Server          |
| Scheduler           |
| Controller Manager  |
| etcd (DB)           |
+---------------------+

+---------------------+
|   Worker Node       |
|---------------------|
| kubelet             |
| kube-proxy          |
| Container Runtime   |
| (Docker / containerd)
+---------------------+
```

---

# Step 4: Control Plane (Brain of Kubernetes)

### 1️⃣ API Server

* Entry point to Kubernetes
* All commands go through it (`kubectl`)
* Validates & processes requests

Example:

```bash
kubectl apply -f pod.yaml
```

➡ goes to **API Server**

---

### 2️⃣ etcd (Key-Value Store)

* Kubernetes **database**
* Stores:

  * Pods
  * Services
  * Configs
  * Secrets
* **Single source of truth**

> If etcd is lost → cluster state is lost

---

### 3️⃣ Scheduler

* Decides **where** to run a Pod
* Picks best worker node based on:

  * CPU
  * Memory
  * Constraints

---

### 4️⃣ Controller Manager

* Watches cluster state
* Makes reality match **desired state**

Example:

* Desired: 3 replicas
* Actual: 2 running
  ➡ Controller creates 1 more Pod

---

# Step 5: Worker Node Components

### 1️⃣ kubelet

* Agent running on every worker node
* Talks to API Server
* Starts/stops containers

---

### 2️⃣ Container Runtime

* Runs containers
* Examples:

  * Docker
  * containerd
  * CRI-O

---

### 3️⃣ kube-proxy

* Handles networking
* Routes traffic to correct Pods

---

# Step 6: Core Kubernetes Objects (MOST IMPORTANT)

Now the real Kubernetes learning starts 🔥

---

## 1️⃣ Pod (Smallest Unit)

* Pod = **one or more containers**
* Containers inside Pod:

  * Share IP
  * Share storage
* Usually **1 container per Pod**

> You never deploy containers directly
> You deploy **Pods**

---

## 2️⃣ Deployment (Most Used)

* Manages Pods
* Provides:

  * Scaling
  * Self-healing
  * Rolling updates

Example:

```yaml
replicas: 3
```

➡ Kubernetes ensures **3 Pods always run**

If one crashes → auto recreated

---

## 3️⃣ ReplicaSet (Behind the Scenes)

* Ensures correct number of Pods
* You rarely create it directly
* Deployment manages ReplicaSet

---

## 4️⃣ Service (Networking)

Pods are:

* Temporary
* IP changes

👉 Service provides:

* Stable IP
* Load balancing

### Service Types:

* **ClusterIP** → internal
* **NodePort** → external via node
* **LoadBalancer** → cloud LB

---

## 5️⃣ ConfigMap & Secret

### ConfigMap

* Store configs (env vars, files)

### Secret

* Store sensitive data

  * passwords
  * API keys

---

# Step 7: How Kubernetes Works (Life Cycle)

Let’s say you deploy an app:

1. You write YAML
2. `kubectl apply`
3. API Server stores in etcd
4. Scheduler selects node
5. kubelet pulls image
6. Container starts
7. Controller keeps monitoring

➡ **Desired State = Reality**

---

# Step 8: YAML – Kubernetes Language

Everything in Kubernetes is **YAML**

Basic structure:

```yaml
apiVersion:
kind:
metadata:
spec:
```

Example:

```yaml
kind: Pod
metadata:
  name: my-app
spec:
  containers:
    - name: app
      image: nginx
```

---

# Step 9: Scaling & Self-Healing

### Scaling:

```bash
kubectl scale deployment myapp --replicas=5
```

### Self-healing:

* Pod crashes?
  ➡ Kubernetes restarts it automatically

---

# Step 10: Rolling Updates (Zero Downtime)

* Deploy new version gradually
* Old Pods replaced one by one
* If failure → rollback

---

# Step 11: Kubernetes for ML / AI (Your Context)

Very important for you 👇

### Common ML use cases:

* Model serving (FastAPI + Torch)
* Batch jobs (training, inference)
* Auto-scaling inference pods
* GPU scheduling

K8s objects used:

* Deployment → model serving
* Job / CronJob → batch inference
* HPA → auto scaling
* PVC → model storage

---

# Step 12: How You Should Learn (Practical Roadmap)

### Phase 1: Basics (Week 1)

* Docker revision
* Kubernetes architecture
* Pod, Deployment, Service

### Phase 2: Hands-on (Week 2)

* Minikube or Kind
* Deploy simple app
* Expose using Service

### Phase 3: Intermediate (Week 3)

* ConfigMap, Secret
* Volumes
* Rolling updates

### Phase 4: Advanced (Week 4+)

* HPA
* Helm
* Ingress
* GPU scheduling
* Production best practices

---

# Step 13: Tools You’ll Use

* `kubectl`
* Minikube / Kind
* Helm
* Docker
* YAML

---

# Final Mental Model (Remember This)

> **Kubernetes does NOT run your app**
> **It manages how your app runs**

Or:

> **You tell Kubernetes WHAT you want**
> **Kubernetes figures out HOW to do it**

---
