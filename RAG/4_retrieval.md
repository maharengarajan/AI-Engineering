# 1. What is retrieval in RAG?

**Retrieval = selecting the best chunks from your corpus given a query**

Goal:

> Maximize **relevant signal**
> Minimize **irrelevant noise**

Retrieval is usually **multi-stage**, not a single search.

---

# 2. Sparse retrieval

### What it is

Keyword-based retrieval using:

* Inverted index
* Term statistics

Classic algorithms:

* **BM25**
* TF-IDF

### How it works (BM25 intuition)

* Matches exact words
* Boosts rare terms
* Penalizes long documents

### Example

Query:

```
"Kafka producer retry backoff"
```

Sparse retrieval excels at:

* `Kafka`
* `retry`
* `backoff`

### Pros

✅ Exact match (IDs, errors, names)
✅ Fast
✅ Interpretable
✅ No embeddings needed

### Cons

❌ No semantic understanding
❌ Synonyms don’t match
❌ Poor conversational handling

### Best used when

* Error codes
* Logs
* Configs
* Legal references
* API names

---

# 3. Dense retrieval

### What it is

Semantic retrieval using **vector similarity** between embeddings.

### How it works

1. Embed query
2. Compare with stored vectors
3. Retrieve nearest neighbors

Similarity metrics:

* Cosine similarity
* Dot product

### Example

Query:

```
"Why does my app keep failing authentication?"
```

Dense retrieval can match:

```
"OAuth token refresh issues"
```

### Pros

✅ Captures meaning
✅ Handles paraphrasing
✅ Great for natural language

### Cons

❌ Misses exact tokens
❌ Weak for rare terms
❌ Less interpretable

### Best used when

* Conversational queries
* FAQs
* Knowledge assistants

---

# 4. Hybrid retrieval (Sparse + Dense)

### What it is

Combining **keyword + semantic** retrieval.

### Why hybrid is the default in production

Because **users mix both styles**:

> “Error 401 when refreshing OAuth token”

Contains:

* Error code → sparse
* Explanation intent → dense

### Common hybrid patterns

### (A) Parallel + merge

```
Dense top-50
Sparse top-50
→ Merge
→ Rerank
```

### (B) Weighted scoring

```
score = α * dense + β * sparse
```

### Pros

✅ Best recall
✅ Best robustness
✅ Handles messy real queries

### Cons

❌ More infra
❌ More tuning

### Best used when

* Enterprise RAG
* Technical docs
* Mixed query types

---

# 5. Metadata-based retrieval

### What it is

Using **metadata filters** to narrow the search space *before* or *during* retrieval.

Metadata examples:

* Document type
* Version
* Team
* Date
* Language
* Access control
* Product

### Example

Query:

```
"How does retry work?"
```

Metadata filters:

```
service = "Kafka"
doc_type = "Design"
version >= 2.0
```

### Pros

✅ Huge precision boost
✅ Faster retrieval
✅ Essential for security & compliance

### Cons

❌ Requires clean metadata
❌ Can over-filter and lose recall

### Best used when

* Large corpora
* Enterprise systems
* Multi-tenant RAG

> **Rule of thumb:** Metadata filtering first, semantic ranking second.

---

# 6. Hierarchical retrieval

### What it is

Retrieve in **levels**:

1. Document-level
2. Section-level
3. Chunk-level

### Example

1. Find relevant documents
2. Find relevant sections inside them
3. Retrieve best chunks

### Pros

✅ Scales to massive corpora
✅ Preserves structure
✅ Reduces noise

### Cons

❌ More complex
❌ More latency

### Best used when

* Very large documents
* Books, manuals
* Research papers

---

# 7. Query-aware / adaptive retrieval

### What it is

Retrieval strategy adapts **based on query type**.

Examples:

* Short keyword query → sparse-heavy
* Long natural language → dense-heavy
* Question about a specific doc → metadata filter

Often implemented via:

* Query classifier
* LLM-based router

### Pros

✅ Better accuracy
✅ Lower cost
✅ Smarter systems

### Cons

❌ Requires routing logic
❌ Needs evaluation

---

# 8. Retrieval augmentation techniques

These sit *around* core retrieval.

### (A) Query expansion

* Add synonyms
* Add related terms
* LLM-generated expansions

### (B) Multi-query retrieval

Generate multiple reformulations:

```
Original query
→ Query 1
→ Query 2
→ Query 3
```

Retrieve for all → merge

### (C) Self-query retrieval

LLM extracts:

* Filters
* Constraints
* Intent

---

# 9. Reranking (critical piece)

### Why reranking exists

Retrieval is optimized for **recall**, not precision.

You might retrieve:

* 50 candidates
* Only 5 are truly relevant

Reranking fixes that.

---

## 9.1 Lexical reranking

* BM25-based scoring
* Cheap
* Limited semantic understanding

---

## 9.2 Cross-encoder reranking

### How it works

Instead of embedding separately:

```
[QUERY] + [DOCUMENT]
→ Transformer
→ Relevance score
```

### Pros

✅ Much higher precision
✅ Deep understanding
✅ Industry standard

### Cons

❌ Slower
❌ Expensive (O(N))

Used only on:

* Top-20 or Top-50 results

---

## 9.3 LLM-based reranking

### What it is

An LLM evaluates relevance directly.

Example prompt:

> “Rank these chunks by relevance to the query.”

### Pros

✅ Highest quality
✅ Can follow task-specific criteria

### Cons

❌ Very expensive
❌ Latency
❌ Non-deterministic

Used when:

* High-stakes answers
* Small candidate sets

---

# 10. Typical production retrieval pipeline

```
User Query
 → Metadata filter
 → Dense retrieval (top 50)
 → Sparse retrieval (top 50)
 → Merge
 → Cross-encoder rerank (top 10)
 → Optional LLM rerank (top 5)
 → Context assembly
 → LLM answer
```

---

# 11. Retrieval evaluation metrics

You **cannot tune retrieval without metrics**.

Common ones:

* Recall@k
* Precision@k
* MRR
* NDCG

Enterprise teams also track:

* Answer faithfulness
* Context relevance
* Hallucination rate

---

# 12. Common mistakes (very common)

❌ Relying on dense only
❌ No metadata filters
❌ No reranking
❌ Too small top-k
❌ No evaluation loop
❌ Assuming vector DB = retrieval solved

---

# 13. Cheat sheet (when to use what)

| Scenario              | Strategy              |
| --------------------- | --------------------- |
| Short keyword queries | Sparse                |
| Natural language      | Dense                 |
| Mixed queries         | Hybrid                |
| Enterprise docs       | Hybrid + metadata     |
| Massive docs          | Hierarchical          |
| High accuracy         | Rerank                |
| Premium RAG           | Hybrid + rerank + LLM |

---

## TL;DR mental model

* **Sparse** → exact words
* **Dense** → meaning
* **Hybrid** → reality
* **Metadata** → precision
* **Reranking** → quality
