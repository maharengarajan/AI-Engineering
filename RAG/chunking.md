## Why chunking matters in RAG
A chunking is a method to break large documents (PDFs, web pages, reports, etc.) into smaller chunks of text before creating embeddings and storing them in a vector database.

## Why chunking matters in RAG

In RAG, chunking affects:

* **Retrieval accuracy** (what gets pulled)
* **Context quality** (what the LLM sees)
* **Latency & cost** (number of vectors)
* **Hallucination rate**

You’re balancing:

> **Too small → loss of context**

> **Too large → poor retrieval precision**

---

# 1. Fixed-size chunking with overlap

### What it is

Split text into chunks of a fixed size (tokens or characters), with overlapping windows.

**Example**

```
Chunk size = 500 tokens
Overlap = 100 tokens
```

```
[1–500]
[401–900]
[801–1300]
```

### Why overlap exists

To avoid cutting important information at boundaries (sentences, definitions, code blocks).

### Pros

✅ Simple
✅ Fast
✅ Deterministic
✅ Works well for uniform text (blogs, docs)

### Cons

❌ Breaks semantic boundaries
❌ Can mix unrelated ideas
❌ Redundant storage due to overlap

### Best used when

* MVPs / prototypes
* Homogeneous text
* You want predictable performance

### Practical tip

* Token-based chunking > character-based
* Typical values:

  * **Text**: 300–800 tokens
  * **Code**: 100–300 tokens
  * **Overlap**: 10–20%

---

# 2. Recursive chunking

### What it is

Chunk text **hierarchically**, respecting structure:

* Paragraphs → sentences → tokens (fallback)

Often implemented as **recursive splitting**:

1. Try split by heading
2. If too large → split by paragraph
3. If still too large → split by sentence
4. If still too large → token split

### Pros

✅ Preserves natural boundaries
✅ Better semantic coherence
✅ Much better than fixed chunking for docs

### Cons

❌ More complex
❌ Depends on clean document structure

### Best used when

* PDFs, manuals, policies, textbooks
* Markdown / HTML documents
* Structured content

### Practical tip

This is the **default recommended approach** in most production RAG systems today.

---

# 3. Document-based chunking

### What it is

Chunks are created based on **document units**, not size:

* One section
* One page
* One heading
* One FAQ entry
* One table

**Example**

* Each FAQ Q+A = one chunk
* Each contract clause = one chunk

### Pros

✅ High semantic integrity
✅ Easy to explain to stakeholders
✅ Very good precision

### Cons

❌ Chunk sizes can vary wildly
❌ Some chunks may be too large
❌ Harder to generalize across doc types

### Best used when

* Legal documents
* FAQs
* SOPs
* Knowledge bases with clear units

### Practical tip

Combine with **max token cap**:

> “One document unit, unless it exceeds 800 tokens → then recursively split.”

---

# 4. Semantic chunking

### What it is

Chunks are created based on **semantic similarity**, not length.

Typical flow:

1. Split text into sentences
2. Embed each sentence
3. Group adjacent sentences with high semantic similarity
4. Stop when similarity drops

### Pros

✅ Very high semantic coherence
✅ Great for narrative or conceptual text
✅ Improves retrieval quality significantly

### Cons

❌ Embedding-heavy (expensive)
❌ Slower ingestion
❌ Harder to tune

### Best used when

* Research papers
* Blogs
* Philosophical / explanatory text
* Dense knowledge content

### Practical tip

Use **cosine similarity threshold + max chunk size** together.

---

# 5. Query-aware chunking

### What it is

Chunking happens **after seeing the query**.

Instead of:

> chunk → embed → retrieve

You do:

> query → identify relevant sections → dynamically chunk only those parts

### Example

Query:

> “How does retry logic work in Kafka producers?”

The system:

* Locates Kafka producer section
* Extracts only retry-related paragraphs
* Chunks *on the fly*

### Pros

✅ Extremely precise
✅ Smaller context → better answers
✅ Reduces noise

### Cons

❌ Requires query understanding
❌ Not suitable for static vector DB ingestion
❌ Higher runtime complexity

### Best used when

* Interactive agents
* Small corpora
* High-precision enterprise search

### Practical tip

Often combined with:

* Keyword search → then chunk
* Hybrid retrieval (BM25 + embeddings)

---

# 6. Metadata-aware chunking

### What it is

Chunks are enriched and/or split using **metadata**:

* Title
* Section
* Author
* Date
* Source
* Access level
* Doc type

Metadata can:

* Influence chunk boundaries
* Be embedded with content
* Be used for filtering before retrieval

### Example

```
Chunk content:
"OAuth token refresh logic..."

Metadata:
{
  "service": "Auth",
  "doc_type": "Design",
  "version": "v2",
  "team": "Platform"
}
```

### Pros

✅ Better filtering
✅ Improves ranking
✅ Essential for enterprise RAG

### Cons

❌ Requires clean metadata
❌ Slightly more ingestion work

### Best used when

* Multi-team documents
* Versioned docs
* Compliance or access-controlled data

### Practical tip

Metadata is **as important as embeddings** in real systems.

---

# 7. LLM-based chunking

### What it is

An LLM decides:

* Where to split
* What belongs together
* What should be summarized or discarded

Prompt example:

> “Split this document into semantically coherent chunks under 500 tokens.”

### Pros

✅ Best semantic quality
✅ Handles messy text well
✅ Adapts to different doc types

### Cons

❌ Expensive
❌ Slower ingestion
❌ Non-deterministic

### Best used when

* High-value documents
* Complex PDFs
* Legal / medical / research content

### Practical tip

Use LLM chunking **offline at ingestion**, not at query time.

---

# 8. Agentic chunking

### What it is

An **agent** decides chunking strategy dynamically using tools, rules, and feedback loops.

The agent may:

* Inspect document type
* Choose chunking strategy
* Re-chunk based on retrieval failures
* Adjust chunk sizes over time

### Example

Agent logic:

```
If doc_type == "API spec":
   use document-based chunking
If retrieval confidence < threshold:
   re-chunk semantically
```

### Pros

✅ Adaptive
✅ Self-improving
✅ Best long-term performance

### Cons

❌ Complex to build
❌ Hard to debug
❌ Requires observability

### Best used when

* Large evolving corpora
* Multi-domain RAG
* AI agents with long lifetimes

### Practical tip

Agentic chunking shines when paired with:

* Retrieval evaluation metrics
* Feedback loops
* Tracing (LangSmith, OpenTelemetry)

---

## Comparison summary (quick table)

| Strategy        | Complexity | Quality | Cost   | Typical Use      |
| --------------- | ---------- | ------- | ------ | ---------------- |
| Fixed + overlap | Low        | ⭐⭐      | Low    | MVPs             |
| Recursive       | Medium     | ⭐⭐⭐⭐    | Low    | Docs             |
| Document-based  | Medium     | ⭐⭐⭐⭐    | Low    | Legal / FAQs     |
| Semantic        | High       | ⭐⭐⭐⭐⭐   | Medium | Research         |
| Query-aware     | High       | ⭐⭐⭐⭐⭐   | Medium | Agents           |
| Metadata-aware  | Medium     | ⭐⭐⭐⭐    | Low    | Enterprise       |
| LLM-based       | High       | ⭐⭐⭐⭐⭐   | High   | Premium RAG      |
| Agentic         | Very High  | ⭐⭐⭐⭐⭐+  | High   | Advanced systems |

---

## Final practical advice (what actually works)

For **most production RAG systems**:

> ✅ Recursive chunking
> ✅ Metadata-aware
> ✅ Optional semantic refinement
> ❌ Avoid pure fixed chunking unless prototyping