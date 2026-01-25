# What is LLMOps (post-deployment)?

**LLMOps** is everything required to:

* Keep your **RAG system reliable**
* Improve **answer quality over time**
* Control **cost, latency, and failures**
* Safely update **models, embeddings, data, prompts**

In short:

> **LLMOps = Operating, monitoring, evaluating, and evolving LLM systems in production**

---

# High-level lifecycle (after deployment)

Once your RAG is live:

```
User Queries
   ↓
Monitoring → Evaluation → Optimization → Retraining / Re-indexing
   ↑___________________________________________________________|
```

Let’s break this into **concrete pillars**.

---

# 1. Observability & Monitoring (MOST IMPORTANT)

If you can’t observe it, you can’t fix it.

## 1.1 What do you monitor in RAG?

### A. System metrics (classic)

* Latency (P50 / P95 / P99)
* Throughput (QPS)
* Error rates
* Timeouts / retries

### B. LLM-specific metrics

* Prompt tokens
* Completion tokens
* Cost per request
* Context window utilization
* Truncation events

### C. Retrieval metrics (RAG-specific)

* Top-K retrieved chunks
* Retrieval latency
* Empty retrievals
* Duplicate chunks
* Chunk size distribution

### D. Quality signals (implicit)

* User clicks
* Regenerations
* Thumbs up/down
* Session abandonment
* Follow-up question rate

📌 **Key idea**: RAG failures are often *retrieval failures*, not LLM failures.

---

## 1.2 What can go wrong (real life)?

| Problem              | Root cause                      |
| -------------------- | ------------------------------- |
| Hallucinations       | Missing or irrelevant retrieval |
| Wrong answers        | Chunking or embedding mismatch  |
| High latency         | Vector DB index, reranker, LLM  |
| High cost            | Too many tokens, large Top-K    |
| Inconsistent answers | Prompt drift or model update    |

---

# 2. Logging & Tracing (RAG-aware)

You must log **every step of the pipeline**.

## What to log per request

```json
{
  "query": "user question",
  "retrieved_docs": [doc_ids],
  "retrieval_scores": [0.82, 0.79],
  "reranked_docs": [doc_ids],
  "final_prompt": "...",
  "llm_response": "...",
  "latency": {
    "retrieval": 120,
    "rerank": 40,
    "generation": 900
  }
}
```

This enables:

* Root cause analysis
* Prompt debugging
* Retrieval tuning
* Offline evaluation

---

# 3. Evaluation (Automatic + Human)

This is where LLMOps differs massively from DevOps.

## 3.1 Offline evaluation (before prod changes)

### Retrieval evaluation

* Recall@K
* Precision@K
* MRR
* Coverage

Example:

> *Does the ground-truth document appear in Top-5 retrievals?*

---

### Generation evaluation

* Faithfulness (answer grounded in context?)
* Relevance
* Completeness
* Toxicity / safety

Usually done via:

* **LLM-as-a-judge**
* Human review for gold sets

---

## 3.2 Online evaluation (in production)

### Implicit signals

* User feedback
* Re-ask rate
* Time spent reading

### Explicit signals

* 👍 / 👎
* Ratings
* Corrections

📌 These signals feed back into **retrieval tuning and prompt updates**.

---

# 4. Prompt & Chain Versioning

Prompts are **production artifacts**, not text files.

## You must version:

* System prompts
* RAG instructions
* Citation rules
* Agent tools logic

Example:

```
prompt_v1 → factual but verbose
prompt_v2 → concise but misses citations
prompt_v3 → balanced
```

### Best practices

* Prompt A/B testing
* Canary rollout (5% traffic)
* Rollback support

---

# 5. Embedding & Index Lifecycle Management

RAG systems break silently when embeddings drift.

## 5.1 When do you re-embed?

* Change embedding model
* Change chunking strategy
* New data schema
* Domain shift (new terminology)

### Example

```
bge-base → text-embedding-3-large
```

→ cosine similarity distribution changes
→ old index becomes suboptimal

📌 **Rule**:

> Embedding model change = full re-index

---

## 5.2 Index versioning

Maintain:

```
index_v1 (old embeddings)
index_v2 (new embeddings)
```

Gradually:

* Shadow test
* Compare retrieval quality
* Switch traffic

---

# 6. Cost Optimization (Very Real in Prod)

LLMOps is also **FinOps**.

## Cost drivers in RAG

| Component       | Cost impact      |
| --------------- | ---------------- |
| Embedding model | Re-index cost    |
| Top-K           | Prompt size      |
| Chunk size      | Token usage      |
| Reranker        | Extra model call |
| LLM choice      | $$$              |

### Common optimizations

* Reduce Top-K after reranking
* Smaller chunks + fewer chunks
* Hybrid retrieval only when needed
* Cache embeddings & responses
* Use smaller LLM for easy queries

---

# 7. Continuous Improvement Loop (Closed Loop)

This is the **heart of LLMOps**.

```
User Queries
   ↓
Logs + Feedback
   ↓
Failure Analysis
   ↓
Fix (prompt / retrieval / data)
   ↓
Redeploy
```

### Example failure → fix

| Issue                   | Fix                       |
| ----------------------- | ------------------------- |
| Missing answers         | Add documents             |
| Wrong section retrieved | Improve chunking          |
| Hallucinations          | Stronger grounding prompt |
| Poor ranking            | Add reranker              |
| Long latency            | Reduce Top-K              |

---

# 8. Safety, Guardrails & Governance

Production LLMs need control.

## Guardrails

* Input validation
* Output filtering
* PII masking
* Refusal handling
* Jailbreak detection

## Governance

* Model approval
* Prompt review
* Data access control
* Audit logs

---

# 9. Deployment Patterns in LLMOps

### Blue-Green deployment

* Old RAG pipeline
* New RAG pipeline
* Switch traffic

### Canary release

* 5% users → new embeddings / prompt

### Shadow testing

* Compare outputs silently
* No user impact

---

# How LLMOps differs from MLOps (important)

| MLOps             | LLMOps                    |
| ----------------- | ------------------------- |
| Model retraining  | Prompt + retrieval tuning |
| Static evaluation | Continuous evaluation     |
| Structured data   | Unstructured text         |
| Model is core     | System is core            |

📌 In RAG:

> **The model is replaceable, the pipeline is not**

---

# Typical LLMOps Tech Stack (example)

* **Observability**: LangSmith, OpenTelemetry
* **Vector DB**: Qdrant / Pinecone
* **Evaluation**: Ragas, LLM-as-judge
* **Prompt mgmt**: Git + prompt registry
* **Deployment**: Kubernetes, Serverless
* **Monitoring**: Grafana, Prometheus

---

# One-line mental model

> **LLMOps = making your RAG system trustworthy, affordable, observable, and improvable in production**

---
