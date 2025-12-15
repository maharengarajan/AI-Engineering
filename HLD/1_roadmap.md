# 🧠 High-Level Design (HLD) Roadmap

## Phase 1: Core Foundations (Must Know)

### 1️⃣ What is HLD?

* Focuses on **architecture**, not code
* Answers:

  * What components exist?
  * How do they communicate?
  * How does the system scale?
  * How is data stored?

👉 Output of HLD:

* Architecture diagram
* Component responsibilities
* Tech choices & trade-offs

---

### 2️⃣ Basic Building Blocks

Learn these **very deeply**:

#### a) Client–Server Architecture

* Web client, mobile client
* API gateway
* Backend services

#### b) Communication

* HTTP/HTTPS
* REST vs gRPC
* WebSockets
* Sync vs async communication

#### c) Databases

* SQL (Postgres, MySQL)
* NoSQL:

  * Key-Value (Redis, DynamoDB)
  * Document (MongoDB)
  * Column (Cassandra)
* CAP theorem

#### d) Caching

* Why cache?
* Redis / Memcached
* Cache-aside pattern
* TTL, eviction policies

---

## Phase 2: Scalability & Reliability (Very Important)

### 3️⃣ Load & Scaling

* Vertical vs Horizontal scaling
* Load balancers

  * L4 vs L7
* Auto-scaling concepts

---

### 4️⃣ Microservices Architecture

* Monolith vs Microservices
* Service discovery
* API Gateway
* Inter-service communication

---

### 5️⃣ Messaging & Async Systems

* Why async?
* Message queues:

  * Kafka
  * RabbitMQ
  * SQS
* Event-driven architecture

Use cases:

* Notifications
* ML inference pipeline
* Logging & metrics

---

### 6️⃣ Data Consistency & Reliability

* ACID vs BASE
* Eventual consistency
* Idempotency
* Retry mechanisms
* Circuit breakers

---

## Phase 3: System Design Concepts (Interview Core)

### 7️⃣ System Design Fundamentals

* Throughput (QPS)
* Latency (P50, P95, P99)
* Availability
* SLA / SLO / SLIs
* Fault tolerance

---

### 8️⃣ Storage & Data Flow Design

* Read-heavy vs write-heavy systems
* Sharding strategies
* Replication
* Data partitioning

---

### 9️⃣ Security (HLD Level)

* Authentication vs Authorization
* OAuth2 / JWT
* Rate limiting
* Secrets management

---

## Phase 4: Cloud & DevOps Awareness

### 🔟 Cloud Primitives

(Cloud-agnostic thinking first)

* Compute: VM, Containers
* Networking: VPC, Subnets
* Storage: Object, Block, File
* Managed DBs

Map later to:

* AWS / Azure / GCP

---

### 1️⃣1️⃣ Containerization & Orchestration

* Docker
* Kubernetes (very important for you)
* Services, Ingress
* ConfigMaps, Secrets

---

## Phase 5: ML System Design (Your Superpower 💪)

### 1️⃣2️⃣ ML-Specific HLD Concepts

This is where you can **stand out in interviews**.

#### ML Training Architecture

* Data ingestion
* Feature store
* Training pipeline
* Model registry

#### ML Inference Architecture

* Batch vs Real-time inference
* Online prediction service
* Model versioning
* A/B testing

#### Model Deployment

* Canary deployment
* Blue-green deployment
* Shadow deployment

---

### 1️⃣3️⃣ MLOps Design Patterns

* CI/CD for ML
* Monitoring:

  * Data drift
  * Concept drift
  * Performance drift
* Rollback strategies

---

## Phase 6: Practice with Real Systems

### 1️⃣4️⃣ Classic HLD Problems

Practice designing:

* URL Shortener
* WhatsApp / Chat App
* YouTube / Video Streaming
* E-commerce system
* Notification system

---

### 1️⃣5️⃣ ML-Oriented HLD Problems (Very Relevant)

Design:

* Real-time recommendation system
* Fraud detection system
* ChatGPT-like system
* RAG-based document search
* ML model deployment on EKS

---

## Phase 7: How to Approach an HLD Interview

### Standard HLD Flow (Memorize This 🔥)

1. Clarify requirements (functional & non-functional)
2. Define scale (users, QPS, data size)
3. High-level architecture diagram
4. Component breakdown
5. Database & storage design
6. Scaling & caching
7. Failure handling
8. Security
9. Trade-offs

---

## 📅 6-Week Practical Learning Plan

| Week | Focus                                  |
| ---- | -------------------------------------- |
| 1    | HLD basics + databases + caching       |
| 2    | Scaling, load balancers, microservices |
| 3    | Messaging, async systems               |
| 4    | Cloud + Kubernetes                     |
| 5    | ML system design                       |
| 6    | Mock interviews + diagrams             |

---

## 🧩 Tools to Practice

* Draw.io / Excalidraw (architecture diagrams)
* Read system design blogs
* Whiteboard thinking (even digitally)

---

## 🎯 Final Advice (Very Important)

* Don’t over-focus on tools
* Think in **components and trade-offs**
* Always justify **why** you chose something

---
