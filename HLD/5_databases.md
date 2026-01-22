## **Databases**

Databases are systems designed to **store, organize, manage, and retrieve data efficiently**. They are a core part of almost every software system—especially backend systems, microservices, and data/ML platforms you’ve been learning about.

Below is a **clear, structured explanation**, starting from basics and moving to advanced concepts.

---

## 1. What is a Database?

A **database** is an organized collection of data that can be:

* Stored permanently
* Queried efficiently
* Updated safely
* Accessed by multiple users/applications

Instead of storing data in files (CSV, JSON, TXT), databases provide:

* Fast access
* Data consistency
* Security
* Concurrency control

---

## 2. Database vs DBMS

| Term                                  | Meaning                            |
| ------------------------------------- | ---------------------------------- |
| **Database**                          | The actual data                    |
| **DBMS (Database Management System)** | Software that manages the database |

Examples of DBMS:

* MySQL
* PostgreSQL
* Oracle
* MongoDB
* Redis

---

## 3. Types of Databases (High Level)

### A. Relational Databases (SQL)

Data is stored in **tables (rows & columns)**.

**Examples**

* MySQL
* PostgreSQL
* Oracle
* SQL Server

**Characteristics**

* Fixed schema
* Strong consistency
* Uses SQL (Structured Query Language)
* Supports ACID properties

**Example**

```text
Users Table
-----------------
id | name | email
1  | Ram  | ram@gmail.com
```

**Best for**

* Banking
* ERP
* Transaction systems
* Applications requiring strong consistency

---

### B. NoSQL Databases

Designed for **large-scale, distributed, and flexible data**.

#### 1. Document Database

Stores data as JSON-like documents.

**Example**

```json
{
  "id": 1,
  "name": "Ram",
  "skills": ["Python", "ML"]
}
```

**Examples**

* MongoDB
* CouchDB

**Use case**

* User profiles
* Content management
* Flexible schema apps

---

#### 2. Key-Value Store

Data stored as key → value pairs.

**Examples**

* Redis
* DynamoDB

**Use case**

* Caching
* Session storage
* Real-time data

---

#### 3. Column-Oriented Database

Stores data column-wise instead of row-wise.

**Examples**

* Cassandra
* HBase

**Use case**

* Time-series data
* High write throughput
* Distributed systems

---

#### 4. Graph Database

Data stored as nodes and relationships.

**Examples**

* Neo4j
* Amazon Neptune

**Use case**

* Social networks
* Recommendation engines
* Fraud detection

---

## 4. ACID Properties (Very Important)

Relational databases follow **ACID**:

| Property        | Meaning                       |
| --------------- | ----------------------------- |
| **Atomicity**   | Transaction is all or nothing |
| **Consistency** | Data remains valid            |
| **Isolation**   | Transactions don’t interfere  |
| **Durability**  | Data survives crashes         |

Example:
Bank transfer → either money is deducted and credited, or nothing happens.

---

## 5. CAP Theorem (Distributed Databases)

In distributed systems, a database can guarantee **only 2 of 3**:

| Term                    | Meaning                        |
| ----------------------- | ------------------------------ |
| **Consistency**         | All nodes see same data        |
| **Availability**        | System always responds         |
| **Partition Tolerance** | Works despite network failures |

Examples:

* **CP** → MongoDB
* **AP** → Cassandra
* **CA** → Traditional RDBMS (single node)

---

## 6. Schema Concepts

### Schema

Defines structure of the data.

* **SQL** → predefined schema
* **NoSQL** → schema-less or flexible

---

## 7. Indexing

Indexes speed up queries.

Without index:

```
O(N) → full table scan
```

With index:

```
O(log N)
```

Example:

```sql
CREATE INDEX idx_email ON users(email);
```

Tradeoff:

* Faster reads
* Slower writes
* Extra storage

---

## 8. Normalization vs Denormalization

### Normalization

* Reduces redundancy
* Improves consistency
* More joins

### Denormalization

* Faster reads
* More duplication
* Used in analytics & NoSQL

---

## 9. Transactions & Locks

Used to maintain consistency when multiple users access data.

Types:

* Row-level lock
* Table-level lock
* Optimistic locking
* Pessimistic locking

---

## 10. Database in Real Systems (Example)

### Typical Web App Flow

```
Client → API → Database
```

### ML / MLOps Context (Relevant for You)

* **Relational DB** → metadata, experiments, configs
* **Object Storage (S3)** → models, datasets
* **Redis** → feature cache
* **Time-series DB** → monitoring metrics

Example:

* MLflow → PostgreSQL (metadata) + S3 (artifacts)

---

## 11. Popular Databases & When to Use

| Use Case        | Database      |
| --------------- | ------------- |
| Transactions    | PostgreSQL    |
| Caching         | Redis         |
| Flexible schema | MongoDB       |
| Analytics       | BigQuery      |
| Time-series     | InfluxDB      |
| Search          | Elasticsearch |

---

## 12. Interview-Friendly Summary

> A database is a structured system for storing and retrieving data efficiently. Relational databases use tables and ensure ACID properties, while NoSQL databases offer scalability and flexibility for distributed systems. Choice of database depends on consistency, scale, and access patterns.

---