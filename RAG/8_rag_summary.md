# 🚀 **RAG (Retrieval-Augmented Generation)

---

## **1. Data Preparation**

### **1.1 Document Intake**

* PDFs, HTML, Word, Markdown, DB Records, APIs
* Extract text + structured fields (title, author, tags)

### **1.2 Text Splitting**

* **Fixed-length splitter** → simple token/char count
* **Recursive splitter** → respects boundaries (e.g., paragraphs, headings)
* **Sliding-window splitter** → overlapping chunks to preserve context
* **Semantic splitter** → embedding-based meaning-aware splitting
* **Document-aware splitter** → PDFs, tables, code, logs, FAQ pairs
  *(Goal: balance chunk size vs. semantic cohesion)*

---

## **2. Embeddings**

### **2.1 Embedding Types**

* **Dense embeddings** → semantic similarity (OpenAI, Bedrock, bge)
* **Sparse embeddings** → keyword relevance (BM25, SPLADE)
* **Hybrid embeddings** → combine dense + sparse for best recall

### **2.2 Embedding Generation**

* Normalize text
* Store: vector + metadata + chunk ID + source ID
* Use **embedding cache** to avoid recomputing repeated text

---

## **3. Indexing**

### **3.1 Vector Index Types**

* **HNSW** → best for semantic dense search
* **IVF (Inverted File Index)** → scalable clustering index
* **PQ/OPQ** → compressed vectors for fast/cheap search
* **Flat Index** → brute-force accuracy (costly)

---

## **4. Retrieval**

### **4.1 Retrieval Modes**

* **Dense search** → semantic similarity (top-k)
* **Sparse search** → keyword search (BM25, TF-IDF)
* **Hybrid search** → weighted dense + sparse (best in practice)

### **4.2 Reranking**

* **MMR** → maximal marginal relevance, reduces duplicates
* **Cross-encoder** → LLM model reranks with high accuracy
* **Relevance filters** → check threshold or cosine similarity

### **4.3 Metadata Filtering**

* Filter by: source, author, date range, document type, tags
* Filter → search → rerank → final result

### **4.4 Query Enhancements**

* **Query expansion** → add synonyms, paraphrases
* **Query decomposition** → break big queries into sub-questions

---

## **5. LLM Pipeline**

### **5.1 Prompt Engineering**

* Retrieval-aware prompts
* Instructions + context + formatting rules
* Include citations
* Add reasoning constraints ("use only provided context")

### **5.2 LLM Choices**

* **OpenAI GPT models**
* **Amazon Bedrock models (Claude, Titan, etc.)**

---

## **6. Vector Database**

* **Qdrant** → open source, HNSW, metadata filtering, hybrid search
* **Pinecone** → managed, scalable
* **FAISS** → local, GPU-based
* **Weaviate** → hybrid + schema search

---

## **7. Caching**

* **Embedding Cache** → avoid re-embedding same text
* **Vector Index Cache** → reuse search results
* **Response Cache** → store LLM outputs for repeated queries
  *(Crucial for latency + cost reduction)*

---

## **8. RAG Evaluation**

### **8.1 Retrieval Metrics**

* **Recall@k** → are the right docs retrieved?
* **Precision@k** → are the retrieved docs relevant?
* **Hit@k** → did any relevant doc appear?

### **8.2 Generation Metrics**

* **Faithfulness** → does model stick to context?
* **Answer Relevance** → is answer relevant to query?
* **Groundedness** → is answer based on retrieved docs?
* **Completeness** → is answer fully addressed?
* **Correctness** → factual correctness vs. ground truth

### **8.3 End-to-End Metrics**

* Success rate (task solved?)
* Latency (retrieval + LLM time)
* Cost (tokens, storage, compute)

### **8.4 Evaluation Tools**

* **Ragas**
* **LLM-as-Judge**
* **SME review** (human expert validation)

---

## **9. Continuous Improvement**

* Tune chunk size
* Switch to hybrid (dense + sparse)
* Use better retriever models
* Improve prompts (structured, chain-of-thought, citations)
* Add reranking
* Collect user feedback
* Add monitoring: accuracy, drift, failures

---
