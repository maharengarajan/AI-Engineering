### **Retrieval-Augmented Generation (RAG)**

It’s an advanced technique in **Generative AI** that improves Large Language Models (LLMs) by combining **retrieval (search)** with **generation**.

### Why RAG?

LLMs (like GPT, LLaMA, etc.) are trained on a fixed dataset. They might not know about private, domain-specific, or latest information. RAG solves this by allowing the model to **fetch relevant data** from external knowledge sources before answering.

### How RAG Works

1. **User Query** → You ask a question.
2. **Retrieval Step** → The system searches for relevant documents/data from an external knowledge base (like a vector database: Pinecone, Weaviate, FAISS, Milvus).
3. **Augmentation** → The retrieved documents are fed into the LLM along with the user query.
4. **Generation Step** → The LLM uses both its training knowledge and the retrieved context to generate a more accurate and grounded response.

### Example

🔹 Without RAG:
*"What is the revenue of Tesla in 2023?"*
→ Model might hallucinate (make up numbers).

🔹 With RAG:
The system retrieves Tesla’s 2023 annual report from a knowledge base, passes the relevant section to the LLM, and the LLM generates a precise answer based on facts.

### Key Benefits

* **Up-to-date**: Can use latest documents.
* **Domain-specific**: Uses your private knowledge base.
* **Reduced hallucination**: Model grounds its answers in retrieved facts.
* **Scalable**: Works well with large document collections.

---
## **Differences b/w Prompt Engineering, Fine Tuning & RAG**    
## **1. Prompt Engineering**

* **What it is**:
  Crafting or structuring prompts (the input text) in a clever way so the LLM gives the desired output.

* **How it works**:
  No model changes. You guide the LLM using techniques like **few-shot learning**, **chain-of-thought prompting**, **system prompts**, or **role instructions**.

* **When to use**:

  * Quick experiments.
  * Small tasks where accuracy isn’t mission-critical.
  * No access to the model weights.

* **Example**:
  Instead of asking: *"Summarize this"* →
  Ask: *"Summarize the following document in 3 bullet points, focusing on financial risks."*

## **2. Fine-Tuning**

* **What it is**:
  Training the base LLM further on your **custom dataset** so it learns domain-specific patterns, vocabulary, and style.

* **How it works**:
  You adjust the weights of the model using supervised training (instruction fine-tuning) or reinforcement learning (RLHF).

* **When to use**:

  * Domain-specific tasks (legal, medical, financial).
  * Need consistent style or format.
  * Repeated queries where prompting alone is not enough.

* **Example**:
  Fine-tuning GPT to always answer in **legal contract language** or **medical diagnosis style**.

## **3. Retrieval-Augmented Generation (RAG)**

* **What it is**:
  Combining an LLM with an **external knowledge base**. Instead of relying only on training data, the model retrieves relevant documents and uses them to generate answers.

* **How it works**:
  Query → Retrieve docs from a vector DB (via embeddings) → Pass docs + query to LLM → Generate grounded response.

* **When to use**:

  * Need **latest / private knowledge** (not in training data).
  * Large document collections.
  * Avoid hallucinations with factual grounding.

* **Example**:
  An insurance chatbot that retrieves the correct policy document section before answering the user’s query.

## **Quick Comparison Table**

| Aspect          | Prompt Engineering               | Fine-Tuning                       | RAG                                           |
| --------------- | -------------------------------- | --------------------------------- | --------------------------------------------- |
| **Effort**      | Low (just text design)           | High (need dataset & training)    | Medium (infra: vector DB, retrieval pipeline) |
| **Cost**        | None (just API calls)            | Expensive (GPU training, storage) | Moderate (infra + embeddings)                 |
| **Flexibility** | Very flexible, but brittle       | Consistent once trained           | Highly flexible (update knowledge instantly)  |
| **Best For**    | General tasks, quick prototyping | Specialized style/format/domain   | Dynamic knowledge + factual accuracy          |
| **Updates**     | Change prompt anytime            | Retrain needed                    | Just add/update docs                          |

---
## 🔑 **Core Components of RAG**

### 1. **Document Store / Knowledge Base**

* Where your reference data lives.
* Can be PDFs, text, databases, websites, APIs, etc.
* This is the "external memory" for the LLM.

📦 Common formats:

* **Vector Databases** (Pinecone, Weaviate, FAISS, Milvus, ChromaDB)
* **Search Indexes** (ElasticSearch, OpenSearch)

### 2. **Text Chunking**

* Long documents are broken into **chunks** (e.g., 500–1000 tokens each).
* Helps in precise retrieval and avoids token overflow.

🔧 Techniques:

* Fixed-length chunking
* Sliding window chunking
* Semantic chunking

### 3. **Embeddings**

* Each chunk is converted into a **dense vector representation** (using an embedding model like OpenAI `text-embedding-ada-002`, Cohere, or Hugging Face models).
* Vectors capture semantic meaning → similar content stays close in vector space.

### 4. **Retriever**

* Given a **user query**, the retriever converts it into an embedding and **finds the most relevant chunks** from the knowledge base.

🔧 Common retrieval methods:

* **Vector similarity search** (cosine similarity, dot product)
* **BM25 / hybrid search** (combine keyword + semantic search)
* **Reranking** (e.g., using cross-encoders to improve relevance)

### 5. **Context Augmentation**

* The retrieved chunks are added to the **user query** before sending to the LLM.
* This is often called the **prompt template** (Query + Context + Instructions).

### 6. **LLM (Generator)**

* The LLM (GPT, LLaMA, Mistral, etc.) receives the **augmented input** and generates a grounded answer.
* It uses its **training knowledge + retrieved context** to respond.

### 7. **Orchestration Layer**

* Middleware that manages the entire flow:

  * Query handling
  * Retrieval logic
  * Prompt formatting
  * Response post-processing

🔧 Frameworks:

* LangChain
* LlamaIndex
* Haystack
* CrewAI / LangGraph (for multi-agent workflows)

### 8. **Feedback & Evaluation (Optional)**

* To measure quality and improve results:

  * **Human feedback** (reinforcement learning)
  * **Automatic metrics** (faithfulness, relevancy, answer completeness)

---
## 🔹 What are **Embeddings**?

* An **embedding** is a numerical representation (vector) of data (text, image, audio, etc.) in a continuous vector space.
* The goal is: **similar things are close together** in that space, and dissimilar things are far apart.
* Example (text):

  * `"dog"` → `[0.23, -0.91, 1.02, …]`
  * `"puppy"` will have a vector **close** to `"dog"`
  * `"car"` will have a **far away** vector

In short: **Embeddings turn meaning into numbers.**


## 🔹 What are **Embedding Models**?

* Embedding models are ML/Deep Learning models trained to **map input data → embedding vectors**.
* For text, these are usually **Transformer-based** models (like BERT, RoBERTa, OpenAI’s text-embedding-ada-002, etc.).
* Types of embeddings:

  * **Word embeddings** (Word2Vec, GloVe, FastText) → each word has a fixed vector.
  * **Sentence/document embeddings** (BERT, Sentence-BERT, OpenAI embeddings) → whole sentences/paragraphs mapped to vectors.
  * **Multimodal embeddings** (CLIP for text + images).

## 🔹 How are Embedding Models Trained?

Different training objectives are used. Common methods:

1. **Word-level training** (older methods):

   * **Word2Vec (CBOW / Skip-gram)** → trained to predict surrounding words (context).
   * **GloVe** → trained on co-occurrence statistics.

2. **Transformer-based training** (modern methods):

   * **Masked Language Modeling (MLM)** → BERT learns embeddings by predicting masked tokens.
   * **Contrastive Learning** → models learn embeddings by making semantically similar pairs close, dissimilar pairs far.

     * Example: `"The cat is sleeping"` vs `"A feline is resting"` → pulled together.
     * `"The cat is sleeping"` vs `"I like pizza"` → pushed apart.
     * Sentence-BERT, CLIP, and OpenAI embeddings use this.

3. **Fine-tuning for similarity tasks**:

   * Pretrained LLM embeddings can be fine-tuned on **specific similarity/search datasets**.
   * Example: Biomedical sentence embeddings trained on PubMed papers.


## 🔹 How to Select the Right Embedding Model?

Depends on **use case** 👇

1. **Semantic Search / RAG**

   * Use **sentence/document embeddings**.
   * Ex: `text-embedding-3-large` (OpenAI), `sentence-transformers/all-MiniLM-L6-v2` (Hugging Face).

2. **Clustering / Topic Modeling**

   * Sentence/document embeddings again.
   * If large dataset → prefer **smaller but efficient models** (like MiniLM, E5-small).

3. **Recommendation systems (matching items)**

   * Need embeddings of both query & items in the **same space**.
   * Ex: CLIP for image ↔ text, or specialized product embeddings.

4. **Domain-specific tasks**

   * Use a domain-trained model (e.g., `BioBERT` for medical, `SciBERT` for scientific text).

5. **Multimodal (Text + Image)**

   * Use models like **CLIP** or OpenAI’s multimodal embeddings.


## 🔹 Practical Guidelines

* **OpenAI embeddings**: Best general-purpose (state-of-the-art, scalable).
* **Sentence-Transformers (SBERT)**: Great open-source choice.
* **Size tradeoff**:

  * Small models = faster, cheaper, less accurate.
  * Large models = slower, more expensive, higher accuracy.

✅ **Rule of Thumb**:

* **General semantic tasks** → start with `sentence-transformers/all-MiniLM-L6-v2` (free, light, good quality).
* **High accuracy needed (production search/RAG)** → OpenAI `text-embedding-3-large`.
* **Domain-specific** → pick a domain-trained embedding model.

## 🔎 What are Text Splitters in RAG?

A **text splitter** is a method to break large documents (PDFs, web pages, reports, etc.) into **smaller chunks of text** before creating embeddings and storing them in a vector database.

## ⚡ Why Text Splitters are Needed

1. **Token Limits of LLMs**

   * LLMs can’t handle very long documents at once (e.g., 4k–128k tokens).
   * Splitting ensures chunks are within embedding + prompt size limits.

2. **Better Retrieval Accuracy**

   * If you embed a whole book as a single vector, a query like *“What is the conclusion in Chapter 3?”* won’t match well.
   * Smaller, semantically meaningful chunks = more precise retrieval.

3. **Reduced Noise**

   * Smaller chunks mean you retrieve only the **relevant context** instead of an entire 100-page document.

4. **Improved Grounding**

   * The retrieved text that goes into the LLM should be focused.
   * Splitting ensures the LLM doesn’t get distracted by irrelevant parts.

## 🛠 Types of Text Splitters

### 1. **Fixed-length Splitter**

* Splits text into chunks of fixed size (e.g., 500 tokens).
* Simple but may cut off in the middle of sentences.

### 2. **Recursive Character Splitter**

* Splits text by **hierarchy** (paragraph → sentence → word).
* Ensures chunks break at natural boundaries.

### 3. **Sliding Window Splitter**

* Overlapping chunks (e.g., 500 tokens with 50-token overlap).
* Keeps context continuity between chunks.

### 4. **Semantic Splitter**

* Uses NLP/embeddings to split at **semantic boundaries** (e.g., topic changes).
* More advanced but computationally heavier.

### 5. **Document-type Aware Splitter**

* Custom splitters based on file type (e.g., PDF by headings, HTML by `<div>` or `<p>`, CSV by rows).

## ✅ Example

Suppose you have a 10,000-word policy document.

* Without splitting: You embed the entire document → query retrieval is poor.
* With splitting (e.g., 500-token chunks with overlap): Queries like *“What’s the claim procedure?”* retrieve only the **relevant policy section**.

## ⚡ Rule of Thumb for Chunking

* **Chunk Size**: 300–1000 tokens (depends on domain).
* **Overlap**: 10–20% (to preserve context across chunks).

👉 In short:
**Text splitters make RAG effective by creating bite-sized, meaningful chunks that can be embedded, retrieved, and fed into the LLM without exceeding limits or losing accuracy.**

## 🔹 What is Semantic Chunking?

Semantic chunking is a **text-splitting technique** where documents are broken into smaller, meaningful pieces (chunks) **based on semantic meaning** instead of just arbitrary rules like fixed character/word length.

Instead of cutting after every *N* characters (which might split a sentence in half), semantic chunking tries to split at **natural boundaries**—sentences, paragraphs, or topic shifts—so that each chunk preserves **coherent meaning**.

## 🔹 Why do we need Semantic Chunking?

When working with **RAG (Retrieval-Augmented Generation)** or any **vector database search**, we split text into chunks, embed them, and store them in a vector database.

But:

* If chunks are too big → embeddings become less precise, retrieval may pull unnecessary text.
* If chunks are too small → embeddings lose context, making retrieval incomplete.

👉 Semantic chunking solves this by balancing **cohesion + retrieval accuracy**.

## 🔹 How Semantic Chunking Works

1. **Parse the text** into logical units (sentences, paragraphs).
2. **Analyze semantic similarity** between consecutive sentences/paragraphs.
3. **Group related ones together** into chunks until a coherence threshold is reached.
4. Ensure each chunk fits within the **embedding model’s token limit**.


## 🔹 Example

Imagine this paragraph:

> "Diabetes is a chronic condition where the body cannot properly process blood sugar. Insulin plays a key role in regulating glucose levels. Without proper treatment, diabetes can cause serious health complications.
>
> Machine learning is increasingly used in healthcare. It can help in predicting diseases and providing personalized treatment."

* If we do **fixed-size chunking (e.g., 100 tokens)** → we might cut across the sentence *"Diabetes is a chronic condition…"* and break meaning.
* With **semantic chunking** →

  * Chunk 1 = "Diabetes is a chronic condition… complications."
  * Chunk 2 = "Machine learning is increasingly used…"

Now, when a user asks: *"How is ML used in healthcare?"* → The system retrieves only the second chunk.


## 🔹 Techniques for Semantic Chunking

1. **Sentence/Paragraph-based splitting** → Use NLP libraries (e.g., spaCy, NLTK).
2. **Semantic similarity clustering** → Group sentences by similarity (using embeddings + cosine similarity).
3. **Topic segmentation algorithms** → TextTiling, C99, or transformer-based segmentation.
4. **Hybrid methods** → Sentence splits + token length limits to ensure embeddings work.

## 🔹 Benefits

* Preserves **context and meaning**.
* Improves **retrieval quality** in RAG.
* Avoids “cut-off” sentences.
* Helps embeddings capture **semantic richness**.

✅ In short: **Semantic chunking = meaning-aware text splitting** → so chunks are not just “pieces of text,” but **coherent knowledge units**.


---
## 🔎 What are Embeddings?

An **embedding** is a way of representing text (words, sentences, or documents) as a vector of numbers in a **high-dimensional space**.

* Instead of storing text as raw strings, embeddings capture **semantic meaning**.
* Similar meanings → vectors are close together.
* Different meanings → vectors are far apart.

Example:

* *"dog"* and *"puppy"* → embeddings close together.
* *"dog"* and *"car"* → embeddings far apart.

## ⚡ Role of Embeddings in RAG

In a **Retrieval-Augmented Generation (RAG)** pipeline, embeddings are used to **connect user queries with relevant chunks** from your knowledge base.

### Workflow:

1. **Create Embeddings for Documents**

   * Split documents into chunks (via text splitters).
   * Convert each chunk into a vector embedding using an embedding model.
   * Store these vectors in a **vector database** (e.g., Pinecone, FAISS, Weaviate, Chroma).

2. **Create Embedding for Query**

   * When a user asks a question, the query is also converted into an embedding.

3. **Similarity Search**

   * The query embedding is compared with stored document embeddings.
   * Similarity metrics (cosine similarity, dot product, Euclidean distance) are used to find the closest matches.

4. **Retrieve & Generate**

   * Top-N relevant chunks are retrieved.
   * Passed to the LLM for grounded answer generation.


## 🛠 Embedding Models Commonly Used

* **OpenAI**: `text-embedding-ada-002`, `text-embedding-3-small`, `text-embedding-3-large`
* **Hugging Face**: `all-MiniLM-L6-v2`, `mpnet-base-v2`, `sentence-transformers` family
* **Cohere**: `embed-english-light-v2`, `embed-multilingual-v3`
* **Google**: Universal Sentence Encoder


## 📊 Example

* Query: *"How do I reset my insurance password?"*
* Embedding → `[0.23, -0.44, 0.89, …]`
* Document chunks (policy manual sections) are also embeddings.
* Vector DB search → finds chunk: *"Password reset is available via the insurance portal settings."*
* This chunk is passed to the LLM → LLM generates grounded response.


## ✅ Why Embeddings are Important in RAG

1. **Semantic Search > Keyword Search**

   * "car insurance" ≈ "auto policy" (captured by embeddings, not keywords).
2. **Efficient Retrieval**

   * Embeddings allow **nearest neighbor search** in vector DBs (fast, scalable).
3. **Language Flexibility**

   * Good embeddings work across synonyms, paraphrases, even multiple languages.
4. **Reduce Hallucination**

   * By grounding answers in retrieved, semantically relevant chunks.


👉 In short:
**Embeddings = the glue that connects user queries to the right document chunks in RAG. Without embeddings, retrieval would just be dumb keyword matching.**

# 🔹 1. Cosine Similarity

* **Definition**: Cosine similarity measures how similar two vectors are, based on the **angle** between them.
* **Range**: -1 to 1

  * `1` → vectors point in the same direction (highly similar)
  * `0` → vectors are orthogonal (no similarity)
  * `-1` → vectors point in opposite directions (opposite meaning)

✅ Formula:

$$
\text{Cosine Similarity} = \frac{A \cdot B}{\|A\| \|B\|}
$$

Where:

* $A \cdot B$ = dot product of vectors
* $\|A\|$ = magnitude of vector A
* $\|B\|$ = magnitude of vector B

📌 Example:

* `"dog"` = `[0.8, 0.2]`
* `"puppy"` = `[0.7, 0.3]`

$$
\cos(\theta) \approx 0.98 \quad \Rightarrow \quad very\ similar
$$


# 🔹 2. Semantic Search

* **Definition**: Search that understands **meaning (semantics)** rather than just matching keywords.
* Uses **embeddings + similarity measures** (like cosine similarity).

✅ Example:
Query: `"capital of France"`

* Keyword search → looks for documents containing `"capital"` AND `"France"`
* Semantic search → finds `"Paris is the capital city of France"` even if `"capital of France"` doesn’t appear exactly

📌 **Pipeline for Semantic Search**:

1. Encode query → embedding
2. Encode documents → embeddings
3. Compare query vs docs using cosine similarity
4. Rank documents by similarity score


# 🔹 3. Similarity Search

* **Definition**: General technique to find the **most similar items** in a dataset, based on vector embeddings.
* Not limited to text → can be used for **images, audio, video, products, users, etc.**

✅ Example:

* Image similarity search: Find images visually similar to a given one.
* Text similarity search: Find sentences close in meaning.
* Recommendation: “Users who liked this movie also liked…” → similarity search on embeddings.

📌 Key difference:

* **Semantic Search** = usually about text meaning.
* **Similarity Search** = broader → works for *any modality* (text, image, audio).


# 🔹 How They Connect

* Embedding model → turns data into vectors
* Cosine similarity (or other distance metrics like Euclidean, dot product) → measures closeness of vectors
* Semantic/Similarity search → use those closeness scores to **rank results**

## Retrieval in RAG
### 🔹 1. What is a Matrix in NLP?

In NLP, text data (words, sentences, documents) needs to be converted into **numerical form** so that ML/Deep Learning models can process it.

* One common way is to represent documents using a **Document-Term Matrix (DTM)** or **Word Embedding Matrix**.
* Each row → a document (or word).
* Each column → a word (or feature).

### 🔹 2. Sparse Matrix

* **Definition:** A matrix where most of the elements are **zero**.
* **Why does it happen in NLP?**

  * Vocabulary in NLP is usually **huge** (thousands or millions of words).
  * But any single document/sentence uses only a **small subset** of those words.
  * Example: If you build a Bag-of-Words (BoW) or TF-IDF representation, the majority of words won’t appear in each document → so most entries are zero.

**Example (BoW for 3 documents, vocab = \[dog, cat, apple, run]):**

| Document | dog | cat | apple | run |
| -------- | --- | --- | ----- | --- |
| Doc1     | 1   | 0   | 0     | 1   |
| Doc2     | 0   | 1   | 0     | 0   |
| Doc3     | 0   | 0   | 2     | 0   |

👉 Here, most values are **0** → sparse matrix.

* **Pros:**

  * Easy to construct with BoW/TF-IDF.
* **Cons:**

  * High memory usage (storing many zeros).
  * Inefficient computations.
  * Curse of dimensionality.

### 🔹 3. Dense Matrix

* **Definition:** A matrix where **most values are non-zero**.
* **Why in NLP?**

  * With **Word Embeddings** (Word2Vec, GloVe, FastText, BERT), each word is mapped to a **dense vector** of fixed size (e.g., 100, 300, 768 dimensions).
  * These vectors are learned so that semantic similarity is captured, and each dimension usually has a meaningful (non-zero) value.

**Example (Embeddings, 3 words, vector size = 4):**

| Word  | v1    | v2    | v3    | v4    |
| ----- | ----- | ----- | ----- | ----- |
| dog   | 0.21  | -0.33 | 0.77  | -0.10 |
| cat   | 0.19  | -0.25 | 0.80  | -0.05 |
| apple | -0.44 | 0.67  | -0.12 | 0.39  |

👉 No large number of zeros → dense matrix.

* **Pros:**

  * Lower dimensional (e.g., 300 instead of 100,000).
  * Captures semantic meaning (dog \~ cat).
  * Efficient to store & compute.
* **Cons:**

  * Harder to interpret individual dimensions.
  * Requires training or pre-trained models.

### 🔹 4. Key Comparison

| Feature          | Sparse Matrix (BoW, TF-IDF) | Dense Matrix (Embeddings)             |
| ---------------- | --------------------------- | ------------------------------------- |
| Representation   | High-dimensional, many 0s   | Low-dimensional, mostly non-zero      |
| Interpretability | Easy (counts, frequencies)  | Hard (latent features)                |
| Memory usage     | Large                       | Small                                 |
| Semantics        | Weak (just counts)          | Strong (captures similarity, context) |
| Example          | BoW, TF-IDF                 | Word2Vec, GloVe, BERT                 |


✅ **In short:**

* **Sparse matrices** = simple, interpretable, but inefficient (common in BoW, TF-IDF).
* **Dense matrices** = compact, semantically rich, efficient (common in embeddings).

## 🔎 What is **Retrieval** in RAG?

In **Retrieval-Augmented Generation (RAG)**, *retrieval* is the process of **fetching relevant information** from an external knowledge source (like a vector database, document store, or API) to provide the LLM with additional context.

Since LLMs can’t “memorize” everything (and their training data is fixed at a certain cutoff), retrieval ensures the model always has **up-to-date, domain-specific, and factual information** before generating an answer.

Think of it like:

* **LLM = a smart student**
* **Retrieval = opening the right book page or notes** before answering a question

## ⚡ Types of Retrieval in RAG

Retrieval can happen in different ways depending on *how you fetch documents*. Here are the main strategies:

### 1. **Vector / Dense Retrieval**

* Uses **embeddings** (numerical vector representations of text).
* Both query and documents are converted into embeddings.
* Similarity (often **cosine similarity**) is used to fetch the closest matches.
* Works well for **semantic search** (understands meaning, not just keywords).
* Example: FAISS, Pinecone, Weaviate, Milvus.

### 2. **Sparse Retrieval (Lexical Retrieval)**

* Based on **exact keyword matching** (like TF-IDF, BM25).
* Doesn’t use embeddings — instead, it finds overlap between query words and document words.
* Very precise when you know the **exact keywords**.
* Example: Elasticsearch, Whoosh.

### 3. **Hybrid Retrieval**

* Combines **dense (semantic)** + **sparse (lexical)** retrieval.
* Benefits:

  * Dense → captures semantic meaning.
  * Sparse → ensures keyword precision.
* Useful when documents have technical terms, codes, or rare words.
* Example: Use BM25 + vector search together.

### 4. **Metadata / Filter-based Retrieval**

* Retrieves documents not only by text similarity but also by **structured filters** (tags, date, category, user ID).
* Example: “Fetch only medical research papers from 2023 related to diabetes.”
* Often combined with vector retrieval in production.

### 5. **Query Expansion / Re-ranking Retrieval**

* **Query Expansion**: Enhances the query (e.g., using synonyms, LLM-generated paraphrases).
* **Re-ranking**: Retrieve many documents first, then use an LLM or a model like **cross-encoder** to re-rank based on deeper semantic relevance.
* Improves quality when initial retrieval is noisy.

### 6. **Multi-hop / Iterative Retrieval**

* Sometimes one round of retrieval is not enough.
* The model may:

  1. Retrieve initial docs.
  2. Generate an intermediate reasoning step.
  3. Use that to **fetch more refined docs**.
* Useful for complex queries needing **step-by-step evidence gathering**.

### 7. **Knowledge Graph Retrieval**

* Instead of vector stores, uses a **graph database** (nodes = entities, edges = relationships).
* Retrieval is based on **graph traversal or reasoning**.
* Useful in domains where **relationships matter** (like biomedical research, supply chain, fraud detection).

### 8. **MMR (Maximal Marginal Relevance) Retrieval**

* MMR is a re-ranking technique used in RAG to balance between:
* Relevance (documents that are highly related to the query)
* Diversity (documents that add new information instead of being redundant).

✅ **Summary Table**

| Retrieval Type           | How it Works            | Best For                     |
| ------------------------ | ----------------------- | ---------------------------- |
| Dense / Vector           | Embeddings + similarity | Semantic meaning             |
| Sparse / Lexical         | Keywords (BM25, TF-IDF) | Exact match, rare terms      |
| Hybrid                   | Dense + Sparse          | Balanced precision + recall  |
| Metadata / Filter        | Filters + attributes    | Context-specific constraints |
| Query Expansion / Rerank | Enhance query / re-rank | Improving relevance          |
| Multi-hop Retrieval      | Iterative steps         | Complex reasoning tasks      |
| Knowledge Graph          | Graph traversal         | Entity relationships         |

## 🔹 1. Sentence-Transformers Cross-Encoders

The **Sentence-Transformers** library (`cross-encoder` module) provides several pretrained cross-encoders specifically for reranking tasks.

### Example Models:

* **`cross-encoder/ms-marco-MiniLM-L-6-v2`**

  * Lightweight (MiniLM, \~22M params)
  * Trained on MS MARCO passage ranking
  * Good balance between **speed & accuracy**

* **`cross-encoder/ms-marco-MiniLM-L-12-v2`**

  * Slightly larger (\~33M params)
  * Higher accuracy than L-6

* **`cross-encoder/ms-marco-electra-base`**

  * Based on ELECTRA
  * Trained for reranking in MS MARCO dataset

* **`cross-encoder/ms-marco-Mpnet-base-v2`**

  * Stronger semantic performance
  * Slower than MiniLM, but more accurate


## 🔹 2. Hugging Face Models for Reranking

You can also use any **BERT/RoBERTa/DeBERTa** fine-tuned on passage ranking datasets (like MS MARCO or TREC).

Examples:

* `bert-base-uncased` fine-tuned on **MS MARCO passage ranking**
* `deberta-v3-large` cross-encoders (state-of-the-art in reranking benchmarks)


## 🔹 3. Example Code (Python, Sentence-Transformers)

```python
from sentence_transformers import CrossEncoder

# Load a pretrained cross-encoder
model = CrossEncoder('cross-encoder/ms-marco-MiniLM-L-6-v2')

# Example query and documents
query = "symptoms of diabetes"
docs = [
    "Diabetes can cause frequent urination and increased thirst.",
    "The capital of France is Paris.",
    "Insulin is used to manage blood sugar levels."
]

# Reranking: each (query, doc) pair gets a score
pairs = [(query, doc) for doc in docs]
scores = model.predict(pairs)

# Sort by relevance
reranked = sorted(zip(docs, scores), key=lambda x: x[1], reverse=True)

print("Reranked results:")
for doc, score in reranked:
    print(f"{score:.4f} - {doc}")
```

**Output (example):**

```
0.9123 - Diabetes can cause frequent urination and increased thirst.
0.7854 - Insulin is used to manage blood sugar levels.
0.1021 - The capital of France is Paris.
```

## 🔎 What is **Query Expansion** in RAG?

In **Retrieval-Augmented Generation (RAG)**, **query expansion** means **modifying or enriching the original user query** so that retrieval fetches **more relevant documents**.

The idea is:

* A user query might be short, vague, or missing keywords.
* If we expand it with **synonyms, paraphrases, or related terms**, retrieval has a better chance of finding the right information.

Think of it as:
👉 User query = “seed”
👉 Expanded queries = “branches” that cover related wording


## ⚡ Types of Query Expansion

### 1. **Synonym / Thesaurus Expansion**

* Replace or add synonyms for query terms.
* Example:

  * Query: “AI jobs”
  * Expanded: “AI jobs OR artificial intelligence careers OR machine learning positions”


### 2. **LLM-based Expansion (Paraphrasing)**

* Use an **LLM** to generate multiple reformulations of the query.
* Example:

  * Query: “How to treat flu naturally?”
  * Expanded:

    * “Home remedies for flu”
    * “Natural treatments for influenza”
    * “Herbal medicine for flu symptoms”


### 3. **Pseudo-Relevance Feedback (PRF)**

* First, run the query as-is.
* Look at top retrieved documents → extract frequent terms → expand the query with those terms.
* Example: Rocchio algorithm in classical IR.

### 4. **Knowledge Graph / Ontology Expansion**

* Use a structured knowledge base (like UMLS in medicine, WordNet in NLP).
* Example:

  * Query: “Myocardial infarction”
  * Expanded: “heart attack” (from ontology mapping).

### 5. **Contextual Expansion**

* Add **metadata filters** or **domain-specific context**.
* Example:

  * Query: “Diabetes treatment”
  * Expanded: “Diabetes treatment in adults” or “Latest research on Type 2 diabetes treatment (2023)”.

## 📌 Example in RAG Flow

User query:

> “COVID cure”

Without expansion:

* Retrieval might miss docs containing **“coronavirus treatment”** or **“SARS-CoV-2 therapy”**.

With expansion:

* Expanded query includes: “COVID treatment”, “coronavirus cure”, “SARS-CoV-2 therapy”.
* Retrieval now gets a **richer, more complete set of docs**.

## ✅ Why Query Expansion Helps RAG?

* Handles **vocabulary mismatch** (user vs document wording).
* Improves **recall** (fetches more useful docs).
* Makes RAG more **robust** when users ask vague or short questions.

But ⚠️:

* Over-expansion can bring **irrelevant docs** (noise).
* Needs balance → often combined with **re-ranking** (e.g., MMR, cross-encoders).

## 🔎 What is **Query Decomposition**?

**Query Decomposition** means **breaking a complex user query into smaller, simpler sub-queries** that can each be answered separately, and then combining the results.

Instead of trying to retrieve everything with one big query, we “decompose” it into parts that are easier to retrieve and reason about.

## ⚡ Why Do We Need Query Decomposition?

* Users often ask **multi-step or complex questions**.
* A single retrieval step might miss relevant documents.
* Decomposition allows the system to **retrieve evidence step by step**, then **stitch it together** into a final answer.

Think of it as:
👉 Complex query = a big problem
👉 Query decomposition = breaking it into smaller problems

## 📌 Examples

### Example 1 – Complex Question

> “Compare the side effects of Metformin and Insulin in treating Type 2 diabetes.”

* **Decomposed queries:**

  1. “Side effects of Metformin in Type 2 diabetes”
  2. “Side effects of Insulin in Type 2 diabetes”
  3. “Comparison of Metformin vs Insulin in diabetes treatment”

Retrieval fetches documents for each, then the LLM synthesizes.


### Example 2 – Multi-hop reasoning

> “Who is the current CEO of the company that invented ChatGPT?”

* Step 1: “Which company invented ChatGPT?” → OpenAI
* Step 2: “Who is the current CEO of OpenAI?” → Sam Altman

Final answer is produced by chaining the sub-queries.


## ⚙️ Types of Query Decomposition

1. **Syntactic decomposition**

   * Break query into **clauses or keywords**.
   * Example: “Impact of climate change on agriculture in India” → \[“climate change AND agriculture”], \[“climate change AND India”].

2. **Semantic decomposition (LLM-driven)**

   * Use an **LLM** to understand the query and generate sub-queries.
   * Great for natural language and complex reasoning.

3. **Iterative / Multi-hop decomposition**

   * Answer part of the query → use that answer as input to the next query.
   * Example: chain-of-thought style retrieval.


## ✅ Benefits in RAG

* Handles **multi-part queries** more effectively.
* Improves **precision** (retrieves specific evidence for each part).
* Supports **reasoning-heavy tasks** (research, legal, medical).

⚠️ Challenge:

* Can be slower (multiple retrieval rounds).
* Needs orchestration (which sub-queries, in what order, how to merge answers).


👉 Query decomposition is often combined with **query expansion** and **multi-hop retrieval** for advanced RAG pipelines.

---
## 🔹 What is Multi-Model RAG?

**Multi-Model Retrieval-Augmented Generation (RAG)** extends the classical RAG framework by allowing retrieval and reasoning over **multiple modalities** (e.g., text, images, audio, video, structured data), not just text.

In a traditional RAG system:

* You have a **retriever** → finds relevant chunks from a knowledge base (usually text embeddings).
* Then a **generator (LLM)** → uses those retrieved chunks + the user’s query to produce the final answer.

In **Multi-Model RAG**, the retriever and generator can work with:

* **Text + Images** → (e.g., scientific papers with diagrams)
* **Text + Tables/Structured Data**
* **Text + Audio/Video metadata**
* **Any combination of modalities**

## 🔹 Key Components

1. **Multi-Modal Data Ingestion**

   * Documents can contain **text + images + structured fields**.
   * Each modality is converted into embeddings using specialized models:

     * Text → LLM embeddings (e.g., OpenAI text-embedding-3-large, BERT, etc.)
     * Images → CLIP / BLIP / SigLIP embeddings
     * Audio → Whisper + embeddings
     * Tables → Tabular embedding models

2. **Multi-Modal Indexing & Storage**

   * Vector DBs (like **Weaviate, Pinecone, Milvus**) that support multi-modal fields store embeddings for each modality.
   * Metadata can be stored in relational/NoSQL databases alongside embeddings.

3. **Multi-Modal Retrieval**

   * Query is processed based on modality:

     * Text queries may retrieve both **text chunks** and **related images**.
     * Image queries can retrieve related text explanations.
   * Cross-modal retrieval:

     * e.g., Ask: *“Show me the MRI scan that matches this report.”*
       → System maps text query → image embeddings.

4. **Fusion Mechanism**

   * Retrieved multi-modal context is **aligned/fused** into a single representation.
   * Techniques: late fusion (separate retrieval then combine), early fusion (combine embeddings), or hierarchical fusion.

5. **Generator (Multi-Modal LLM)**

   * Models like **GPT-4o, Gemini, Claude with vision, LLaVA, Kosmos-2, BLIP-2** can **consume multiple modalities**.
   * They take the retrieved text+image/audio chunks and generate a **context-aware response**.

## 🔹 Example Use Cases

1. **Healthcare** → Ask about a radiology report → retrieves **X-ray image + text notes**, LLM explains findings.
2. **Legal/Contracts** → Ask about a clause → retrieves **contract text + tables + referenced diagram**.
3. **E-commerce** → Query product by uploading an image → retrieves **matching product descriptions + price tables**.
4. **Education** → Ask “explain this math problem” with an image → retrieves **textual solutions + formula diagrams**.

## 🔹 Benefits

* Richer knowledge grounding (not limited to text).
* Better reasoning when **text alone is insufficient**.
* Enables **cross-modal search** (image → text, text → image, etc.).

## 🔹 Challenges

* **Embedding alignment** across modalities (text vs. image space).
* **Storage cost**: multiple embeddings per document.
* **Fusion complexity**: deciding how to merge multi-modal signals.
* **Model requirements**: need multi-modal LLMs (not all LLMs can process images/audio).

✅ In short:
**Multi-Model RAG = Classical RAG but extended to handle multiple modalities (text, images, audio, video, structured data).**
It requires multi-modal retrievers + multi-modal LLMs, making it useful for real-world applications where information is not purely textual.

---
## 🔹 What is an AI Agent?

An **AI Agent** is an autonomous (or semi-autonomous) system that:

1. **Perceives** its environment (through data, prompts, APIs, sensors, etc.).
2. **Decides** what action to take (reasoning/planning).
3. **Acts** on the environment (via tools, APIs, code execution, or user interaction).
4. **Learns/Adapts** from feedback.

Formally, an AI Agent follows the loop:
**Perception → Reasoning/Planning → Action → Feedback → (loop again)**

### Examples:

* **Customer Support Agent** → retrieves knowledge base articles and replies.
* **Research Agent** → searches papers, summarizes, and cites references.
* **DevOps Agent** → monitors logs, restarts failing services, deploys code.

In LLM-world (LangChain, CrewAI, AutoGen, etc.), an agent is often an LLM **with memory + tools** that can decide what step to take next.

## 🔹 What is Agentic AI?

**Agentic AI** is a broader concept. It refers to AI systems designed to act with **agency** — meaning they can **plan, take initiative, and pursue goals** rather than just respond passively.

Key traits of **Agentic AI**:

* **Goal-driven** → not just Q\&A, but can **pursue a multi-step objective**.
* **Tool-using** → can interact with APIs, databases, web, or software.
* **Autonomous reasoning** → breaks down tasks, decomposes problems, chooses best actions.
* **Collaboration** → can work with humans or other agents (multi-agent systems).

It’s basically the **next evolution of AI** beyond simple chatbots.

## 🔹 Difference Between AI Agents vs Agentic AI

| Feature    | **AI Agent**                                           | **Agentic AI**                                                                            |
| ---------- | ------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| Definition | A software entity that perceives, reasons, and acts    | A paradigm where AI is designed to have **agency** (goal-directed, proactive, tool-using) |
| Scope      | Specific system (customer support agent, coding agent) | Broader trend in AI research/industry                                                     |
| Autonomy   | May be narrow/single-purpose                           | Strong focus on **autonomy & initiative**                                                 |
| Example    | LangChain Agent that calls a calculator API            | AutoGPT, Devin (AI software engineer), CrewAI multi-agent collaboration                   |

## 🔹 Real-World Analogy

* **AI Agent** = An employee who follows a checklist.
* **Agentic AI** = An employee who understands the company’s goals, makes their own plans, talks to other departments, and executes without micromanagement.

## 🔹 Examples of Agentic AI in Action

* **AutoGPT / BabyAGI** → autonomous LLMs that break down goals and loop until completion.
* **Devin AI** (by Cognition) → software engineer AI that can plan tasks, write code, test, and fix issues.
* **Healthcare AI assistants** → monitor patient data, proactively alert doctors, suggest treatment plans.

✅ **In short:**

* **AI Agent** = an entity that acts in an environment with perception + reasoning + action.
* **Agentic AI** = the broader **approach of building AI with agency** (autonomy, goals, initiative, collaboration).

---
## 1. **React Agent in AI (Agentic AI / LLMs)**

In AI, **React** refers to the **"ReAct" framework** (short for *Reason + Act*) — a method for building **LLM-based agents**.

### 🔹 What is ReAct?

ReAct (introduced in the 2022 paper ["ReAct: Synergizing Reasoning and Acting in Language Models"](https://arxiv.org/abs/2210.03629)) is a framework where LLMs are used as **agents** that can:

* **Reason** → Think through intermediate steps, explain logic.
* **Act** → Call tools, APIs, or external functions based on reasoning.

Instead of just producing a final answer, the agent alternates between:

1. **Thought** – internal reasoning (not shown to the user).
2. **Action** – deciding what to do (e.g., call a calculator, query a database, fetch an API).
3. **Observation** – taking in results from the environment.
4. **Repeat** until it reaches a final **Answer**.

This makes the agent **interactive, tool-using, and goal-oriented**.

### 🔹 Example of a React Agent

User: *“What’s the current temperature in New York, and is it warmer than yesterday?”*

* **Thought**: I need to fetch today’s temperature.
* **Action**: Call Weather API.
* **Observation**: Today = 28°C.
* **Thought**: I need yesterday’s weather data.
* **Action**: Call Weather API (yesterday).
* **Observation**: Yesterday = 25°C.
* **Thought**: Compare values.
* **Answer**: *Yes, it is warmer today by 3°C.*