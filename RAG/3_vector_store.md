## 1. What is a Vector Database?

A **vector database** stores **embeddings** (high-dimensional numeric vectors) and allows **fast similarity search**.

Instead of:

```sql
WHERE name = "john"
```

You ask:

> “Find vectors *closest* to this vector in meaning”

Mathematically:

* Each item → vector of 128 / 384 / 768 / 1536 dims
* Query → vector
* Similarity → cosine / dot product / L2 distance

### Why normal databases fail here

* B-trees, hash indexes → exact match
* Vector search → **approximate nearest neighbor (ANN)**
* Brute force = O(N × D) → impossible at scale

👉 Vector DBs exist to solve **fast ANN search at scale**

---

## 2. High-level Vector Search Architecture

Typical flow:

```
Text / Image / Audio
        ↓
Embedding model (OpenAI, BGE, Instructor, etc.)
        ↓
Vector (e.g. 1536 dims)
        ↓
Vector DB
   ├── Vector index (ANN)
   ├── Metadata store (filters)
   └── Storage (disk + RAM)
        ↓
Top-K similar vectors
```

**Key features every serious vector DB must handle:**

* Fast ANN search
* Metadata filtering (hybrid search)
* Incremental inserts
* Persistence + replication
* Horizontal scaling

---

## 3. How Vector Indexing Works Internally

This is the most important part.

### Exact search (baseline)

```text
For each vector:
    compute distance(query, vector)
Sort and return top K
```

❌ Too slow beyond ~100K vectors

---

## 4. Core ANN Indexing Algorithms

### 4.1 HNSW (Hierarchical Navigable Small World Graph)

👉 **Used by both Qdrant and Pinecone**

#### Concept

* Vectors are nodes in a graph
* Edges connect “similar” vectors
* Search = graph traversal instead of scanning all points

#### Structure

```
Level 3: very few nodes (long jumps)
Level 2
Level 1
Level 0: dense graph (fine-grained)
```

#### Search flow

1. Start at top layer
2. Greedily move closer to query
3. Drop down layers
4. Final refinement at level 0

**Complexity**

* Search: `O(log N)`
* Insert: `O(log N)`

#### Why HNSW is so popular

✅ High recall
✅ Very fast
❌ Memory heavy (graph in RAM)

---

### 4.2 IVF (Inverted File Index)

Used heavily in FAISS-based systems.

#### Idea

* Cluster vectors (k-means)
* Store vectors per cluster
* Query only nearby clusters

```
Query → nearest centroid → scan vectors in that cluster
```

Trade-offs:

* Less memory
* Lower recall unless tuned carefully

---

### 4.3 PQ (Product Quantization)

Used to compress vectors.

* Split vector into chunks
* Quantize each chunk
* Store compressed codes

Good for:

* Massive scale
* Lower RAM usage

Bad for:

* Precision
* Rebalancing

---