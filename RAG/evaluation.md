# RAG Evaluation – Retrieval Stage

## Why retrieval evaluation is critical

In RAG:

> **Bad retrieval → bad generation (even with GPT-5-level models)**

If the correct information is **not retrieved**, the generator:

* Hallucinates
* Gives partial answers
* Sounds confident but is wrong

So retrieval evaluation answers **one core question**:

> *Did my retriever fetch the right information, in the right order, in the top-K results?*

---

## 1️⃣ What exactly are we evaluating in retrieval?

Retrieval evaluation focuses on:

| Dimension                        | What it measures                            |
| -------------------------------- | ------------------------------------------- |
| **Relevance**                    | Are retrieved chunks relevant to the query? |
| **Coverage**                     | Are all necessary facts retrieved?          |
| **Ranking quality**              | Are the most relevant chunks ranked higher? |
| **Noise**                        | How much irrelevant info is included?       |
| **Recall vs Precision tradeoff** | Did we miss anything important?             |

---

## 2️⃣ Ground Truth for Retrieval Evaluation

Before metrics, you need **ground truth**.

### Ground truth can be created by:

1. **Human labeling**

   * For each query → mark which chunks/docs are relevant
2. **Synthetic QA generation**

   * Generate Q/A pairs from documents
   * Link answer → source chunks
3. **Heuristic rules**

   * If answer span exists in chunk → relevant
   * Common in early-stage systems

Example ground truth:

```text
Query: "What is the retention policy for logs?"
Relevant chunks: [chunk_12, chunk_19]
```

⚠️ Retrieval evaluation **without ground truth is guesswork**.

---

## 3️⃣ Core Retrieval Metrics (Most Important Section)

---

## 🔹 3.1 Recall@K (MOST IMPORTANT)

### Definition

> **Did we retrieve at least one relevant chunk in top-K results?**

### Formula

```text
Recall@K = (# relevant retrieved in top K) / (total relevant)
```

### Example

* Relevant chunks: `[A, B]`
* Top-5 retrieved: `[C, A, D, E, F]`

```text
Recall@5 = 1 / 2 = 0.5
```

### Why Recall@K matters most in RAG

* Generator can **ignore noise**
* Generator **cannot recover missing info**

👉 **High recall is more important than high precision** for RAG.

### Typical values

| Use case        | Target Recall@K |
| --------------- | --------------- |
| Enterprise RAG  | 0.85 – 0.95     |
| Legal / Medical | > 0.95          |
| Chatbots        | ~0.80           |

---

## 🔹 3.2 Precision@K

### Definition

> How many of the retrieved chunks are actually relevant?

### Formula

```text
Precision@K = (# relevant retrieved in top K) / K
```

### Example

Top-5 retrieved: `[A, B, C, D, E]`
Relevant: `[A, C]`

```text
Precision@5 = 2 / 5 = 0.4
```

### In RAG context

* Precision matters **after recall is acceptable**
* Low precision → more tokens → higher cost → slower inference

### Practical insight

> RAG systems often accept **low precision (0.2–0.4)** if recall is high.

---

## 🔹 3.3 Hit Rate / Success@K

### Definition

> Did we retrieve **any** relevant chunk in top-K?

### Formula

```text
Hit@K = 1 if ≥1 relevant retrieved else 0
```

Average over all queries.

### Example

* Query 1 → hit
* Query 2 → miss
* Query 3 → hit

```text
Hit@5 = 2 / 3 = 0.67
```

### When to use

* Binary evaluation
* Early-stage systems
* Monitoring production regressions

---

## 🔹 3.4 Mean Reciprocal Rank (MRR)

### Definition

> How early does the **first relevant chunk** appear?

### Formula

```text
MRR = avg(1 / rank_of_first_relevant)
```

### Example

| Query | First relevant rank | Reciprocal |
| ----- | ------------------- | ---------- |
| Q1    | 1                   | 1.0        |
| Q2    | 3                   | 0.33       |
| Q3    | 10                  | 0.1        |

```text
MRR = (1 + 0.33 + 0.1) / 3 = 0.48
```

### Why MRR matters

* Important if:

  * You only pass **top-1 or top-3** chunks to LLM
  * You use **context window limits**

---

## 🔹 3.5 nDCG@K (Ranking Quality)

### Definition

> Measures ranking quality considering **graded relevance**

### Relevance levels:

```text
3 = highly relevant
2 = partially relevant
1 = weakly relevant
0 = irrelevant
```

### Why nDCG is powerful

* Penalizes relevant chunks appearing lower
* Rewards correct ordering

### When to use

* Hybrid search (dense + sparse)
* Reranking evaluation
* Search quality tuning

---

## 4️⃣ Sparse vs Dense vs Hybrid Retrieval Evaluation

### 🔹 Sparse (BM25)

* Strengths:

  * High precision
  * Keyword matching
* Weakness:

  * Low semantic recall

Metrics to focus on:

* Recall@K
* Hit@K

---

### 🔹 Dense (Embeddings)

* Strengths:

  * Semantic similarity
* Weakness:

  * Can retrieve “semantically close but wrong” chunks

Metrics:

* Recall@K
* MRR
* nDCG

---

### 🔹 Hybrid Retrieval

```text
score = α * dense + β * sparse
```

Evaluation focus:

* Compare:

  * Dense only
  * Sparse only
  * Hybrid
* Look at:

  * Recall improvement
  * Precision drop
  * nDCG gain

⚠️ Weighted scoring itself is **NOT reranking**
It’s **score fusion at retrieval time**.

---

## 5️⃣ Reranking Evaluation (Retrieval Stage)

Reranking is still part of **retrieval evaluation**.

### Flow

```text
Retrieve top 50 → Reranker → top 10
```

### Evaluate:

1. Recall@50 (before rerank)
2. Recall@10 (after rerank)
3. MRR improvement
4. nDCG improvement

### Ideal behavior

* Recall stays same
* Precision ↑
* MRR ↑
* nDCG ↑

---

## 6️⃣ Metadata-aware Retrieval Evaluation

### Example filters:

* Date
* Document type
* Tenant / user

Evaluation questions:

* Are relevant docs filtered out?
* Recall drop due to metadata constraints?

You often track:

```text
Recall@K_with_filter vs Recall@K_without_filter
```

---

## 7️⃣ Online vs Offline Retrieval Evaluation

### Offline

* Pre-labeled queries
* Stable metrics
* Used during development

### Online (Production)

* Click-through
* User follow-up questions
* Answer correction signals

Example proxy:

```text
User asked same question again → retrieval failure
```

---

## 8️⃣ Common Retrieval Evaluation Mistakes 🚨

1. Optimizing precision instead of recall
2. Evaluating only top-1
3. No ground truth
4. Ignoring ranking order
5. Mixing retrieval & generation metrics

---

## 9️⃣ Practical Baseline Targets

| Metric    | Good baseline |
| --------- | ------------- |
| Recall@10 | ≥ 0.90        |
| Hit@10    | ≥ 0.95        |
| MRR       | ≥ 0.5         |
| nDCG@10   | ≥ 0.6         |

---

# 🔶 **Response Quality Evaluation (Generation Evaluation)**

Goal: **Did the LLM give a correct, grounded, and complete answer?**

### Key metrics:

---

### **(A) Faithfulness**

Does the answer stick to the given context?
No hallucinations?

Example:
Context says: “Leave policy is 12 days.”
LLM says: “Leave policy is 14 days.” → **unfaithful**

---

### **(B) Answer Relevance**

Does the answer actually address the question?

User:
"What is the refund policy?"
LLM:
"Refund policy varies by department..." (but answer is vague) → low relevance

---

### **(C) Groundedness**

Is every key fact supported by retrieved context?

Very popular in enterprise RAG evaluation.

---

### **(D) Completeness**

Did the answer cover all important details?

Example:
User asks: "Explain maternity leave policy."
LLM explains duration but misses eligibility → incomplete

---

### **(E) Correctness**

Is the answer factually correct?

Sometimes measured by humans or LLM-as-judge.

---


## **RAG Evaluation Summary**

### **Retrieval Metrics**

* **Recall@k** → are the right docs retrieved?
* **Precision@k** → are the retrieved docs relevant?
* **Hit@k** → did any relevant doc appear?

### **Generation Metrics**

* **Faithfulness** → does model stick to context?
* **Answer Relevance** → is answer relevant to query?
* **Groundedness** → is answer based on retrieved docs?
* **Completeness** → is answer fully addressed?
* **Correctness** → factual correctness vs. ground truth

### **End-to-End Metrics**

* Success rate (task solved?)
* Latency (retrieval + LLM time)
* Cost (tokens, storage, compute)

### **Evaluation Tools**

* **Ragas**
* **LLM-as-Judge**
* **SME review** (human expert validation)

---