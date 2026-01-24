## 1. What are embeddings in RAG

An **embedding** is a vector (list of numbers) that represents the **meaning** of text.

In RAG:

* Documents → embeddings → vector DB
* Query → embedding → similarity search
* Top-k chunks → LLM context

The quality of this step directly impacts:

* Recall (did we fetch the right info?)
* Precision (did we fetch only relevant info?)
* Hallucinations

---

# 2. Dense embeddings

### What they are

Dense embeddings are **neural vectors** where:

* Every dimension has a value
* Meaning is encoded implicitly
* Similar meanings → close vectors

**Example**

```
"How to reset password"
→ [0.012, -0.98, 0.45, ...]
```

### How retrieval works

Usually **cosine similarity** or **dot product**:

```
similarity(query_vector, doc_vector)
```

### Pros

✅ Captures semantic meaning
✅ Handles synonyms & paraphrases
✅ Works well with natural language
✅ Core of modern RAG

### Cons

❌ Weak at exact matches (IDs, error codes)
❌ Can miss rare keywords
❌ Hard to explain why something matched

### Best used when

* Natural language queries
* FAQs, docs, knowledge bases
* User-facing assistants

### Popular dense models

* OpenAI `text-embedding-3-large`
* BAAI `bge-*`
* E5, GTE
* Cohere embeddings

---

# 3. Sparse embeddings

### What they are

Sparse embeddings are **keyword-based vectors**:

* Very high dimensional
* Mostly zeros
* Each dimension ≈ token/term

Classic example:

* **TF-IDF**
* **BM25**

**Example**

```
"Kafka producer retries"
→ { kafka: 1.3, producer: 2.1, retries: 3.4 }
```

### How retrieval works

* Exact or near-exact keyword matching
* Inverted index

### Pros

✅ Excellent for exact matches
✅ Handles IDs, error codes, names
✅ Highly interpretable
✅ Very fast

### Cons

❌ No semantic understanding
❌ Synonyms don’t match
❌ Poor for conversational queries

### Best used when

* Technical docs
* Logs
* Error codes
* Part numbers
* Compliance search

---

# 4. Dense vs Sparse (intuition)

| Query                         | Dense | Sparse |
| ----------------------------- | ----- | ------ |
| “How do I reset my password?” | ✅     | ❌      |
| “OAuth token refresh failure” | ⚠️    | ✅      |
| “Error 403 when calling API”  | ❌     | ✅      |
| “Why does auth keep failing?” | ✅     | ❌      |

👉 **Neither is sufficient alone** in real systems.

---

# 5. Hybrid embeddings (Dense + Sparse)

### What they are

Hybrid retrieval **combines dense and sparse signals**.

Two common approaches:

### (A) Parallel retrieval + merge

1. Dense search (vector DB)
2. Sparse search (BM25)
3. Merge & rerank results

### (B) Single hybrid index

Some DBs (Weaviate, Pinecone hybrid) store:

* Dense vector
* Sparse vector
  And score both together.

### Pros

✅ Best recall
✅ Best precision
✅ Covers both semantic & keyword needs
✅ Industry standard for production RAG

### Cons

❌ More infra
❌ More tuning
❌ Slightly higher latency

### Best used when

* Enterprise RAG
* Technical + natural language queries
* High accuracy required

### Practical scoring example

```
final_score =
  0.7 * dense_similarity +
  0.3 * sparse_score
```

---

# 6. Learned sparse embeddings (modern sparse)

This is important and often missed 👇

### What they are

Neural models that produce **sparse vectors**, not classic TF-IDF.

Examples:

* SPLADE
* uniCOIL

### Why they matter

They:

* Expand queries with related terms
* Still keep sparsity
* Perform better than BM25

### Example

Query:

```
"car repair"
```

Sparse expansion:

```
car, auto, vehicle, mechanic, repair, fix
```

### Pros

✅ Better than BM25
✅ Still explainable
✅ Great hybrid partner

### Cons

❌ More compute
❌ Less common tooling

---

# 7. Embedding granularity & alignment

### Key idea

**Query embeddings and document embeddings must be aligned.**

Mistakes:

* Using different models
* Using different preprocessing
* Using different languages

### Best practices

* Same embedding model for query & docs
* Same normalization
* Same chunk size assumptions

---

# 8. Multilingual embeddings

### Why needed

If:

* Users ask in one language
* Docs are in another

### Options

* Multilingual dense models (e5-multilingual, bge-m3)
* Translate query → embed
* Translate docs → embed

### Trade-off

| Method                        | Quality | Cost |
| ----------------------------- | ------- | ---- |
| Multilingual embeddings       | ⭐⭐⭐⭐    | Low  |
| Translation + mono embeddings | ⭐⭐⭐⭐⭐   | High |

---

# 9. Reranking (embeddings alone are not enough)

Embeddings are **recall-oriented**.

Typical pipeline:

1. Retrieve top-50 using embeddings
2. Rerank top-50 using:

   * Cross-encoder
   * LLM reranker
3. Send top-5 to LLM

This dramatically improves answer quality.

---

# 10. End-to-end embedding pipeline in RAG

**Production-grade flow**

```
Documents
 → Chunking
 → Dense embedding
 → Sparse embedding
 → Vector DB (hybrid index)

Query
 → Dense embedding
 → Sparse representation
 → Hybrid retrieval (top 50)
 → Reranker
 → LLM
```

---

# 11. Common embedding mistakes (very common)

❌ Chunk too large → diluted embeddings
❌ Chunk too small → loss of meaning
❌ Pure dense only → misses exact info
❌ No reranking → noisy context
❌ Wrong similarity metric
❌ No evaluation (precision@k, recall@k)

---

# 12. When to choose what (cheat sheet)

| Use case       | Recommendation       |
| -------------- | -------------------- |
| MVP            | Dense only           |
| Blogs / FAQs   | Dense + reranker     |
| Enterprise RAG | Hybrid + reranker    |
| Logs / errors  | Sparse + hybrid      |
| Multilingual   | bge-m3 / e5          |
| Agentic RAG    | Hybrid + query-aware |

---
