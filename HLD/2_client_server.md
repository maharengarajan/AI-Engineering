## *Client–Server Architecture*

Client–Server Architecture is a **distributed computing model** where tasks and responsibilities are divided between **clients** (requesters of services) and **servers** (providers of services). It is one of the most widely used architectures in computer networks, web applications, and enterprise systems.

---

## 1. Basic Concept

* **Client**

  * A device or application that **initiates requests**
  * Examples: Web browser, mobile app, desktop application
* **Server**

  * A powerful system that **processes requests** and returns responses
  * Examples: Web server, database server, application server

📌 **Key Idea:**
Clients request → Servers process → Servers respond

---

## 2. How It Works (Request–Response Model)

1. Client sends a request (HTTP, FTP, SQL query, etc.)
2. Server receives and processes the request
3. Server sends back the response
4. Client displays or uses the result

**Example:**
Browser requests a webpage → Web server sends HTML/CSS/JS → Browser renders page

---

## 3. Components of Client–Server Architecture

### 1. Client

* User interface
* Sends requests
* Handles presentation logic
* Examples: Chrome browser, Android app

### 2. Server

* Handles business logic
* Manages resources (data, files, services)
* Provides security and authentication

### 3. Network

* Communication medium (LAN, WAN, Internet)
* Uses protocols like HTTP, HTTPS, TCP/IP

---

## 4. Types of Client–Server Architecture

### 1️⃣ Two-Tier Architecture

Client ↔ Server

* Client communicates directly with the server
* Common in small applications

**Example:**

* Client: UI + logic
* Server: Database

📌 Used in desktop DB applications

---

### 2️⃣ Three-Tier Architecture

Client ↔ Application Server ↔ Database Server

* Separates presentation, business logic, and data
* More scalable and secure

**Layers:**

* Presentation Layer (Client)
* Business Logic Layer (App Server)
* Data Layer (DB Server)

📌 Used in web applications

---

### 3️⃣ N-Tier Architecture

Client ↔ Multiple Servers

* More than three layers
* Highly scalable and fault-tolerant

**Example:**

* Load balancer
* API gateway
* Microservices
* Database clusters

📌 Used in enterprise and cloud systems

---

## 5. Common Client–Server Protocols

| Protocol     | Purpose                    |
| ------------ | -------------------------- |
| HTTP / HTTPS | Web communication          |
| FTP          | File transfer              |
| SMTP         | Email sending              |
| POP3 / IMAP  | Email retrieval            |
| TCP/IP       | Reliable data transmission |

---

## 6. Advantages of Client–Server Architecture

✅ Centralized data management
✅ Easier maintenance and updates
✅ Better security control
✅ Scalable (add more servers or clients)
✅ Efficient resource sharing

---

## 7. Disadvantages

❌ Server can be a single point of failure
❌ Higher cost (server infrastructure)
❌ Network dependency
❌ Server overload if many clients connect simultaneously

---

## 8. Real-World Examples

* **Web Applications:** Browser (client) ↔ Web server (server)
* **Email Systems:** Email client ↔ Mail server
* **Banking Systems:** ATM ↔ Bank server
* **ML Systems:** Inference client ↔ Model-serving API (FastAPI, Flask)

👉 In **MLOps**, a trained model is deployed as a **server**, and applications consume predictions as **clients**.

---

## 9. Client–Server vs Peer-to-Peer (P2P)

| Feature     | Client–Server | P2P           |
| ----------- | ------------- | ------------- |
| Control     | Centralized   | Decentralized |
| Security    | High          | Lower         |
| Scalability | Good          | Limited       |
| Example     | Web apps      | BitTorrent    |

---

## 10. Summary

* Client–Server architecture divides responsibilities
* Clients request services; servers provide them
* Supports scalability, security, and centralized control
* Foundation of modern web, cloud, and ML systems

---