## **1. Bag of Words (BoW)**

**Definition:**
Bag of Words is a way to represent text as a collection (bag) of its words, **ignoring grammar and word order**, but keeping multiplicity (how many times each word occurs).

**How it works:**

1. Take all unique words in your corpus (all your documents) → this becomes the **vocabulary**.
2. For each document, create a vector where each element counts how many times a word from the vocabulary appears in the document.

**Example:**

Suppose we have two sentences:

* Doc1: "I love AI"
* Doc2: "AI is amazing"

**Step 1: Vocabulary** → {I, love, AI, is, amazing}

**Step 2: Represent documents as vectors**

| Word | I | love | AI | is | amazing |
| ---- | - | ---- | -- | -- | ------- |
| Doc1 | 1 | 1    | 1  | 0  | 0       |
| Doc2 | 0 | 0    | 1  | 1  | 1       |

**Pros:**

* Simple to implement
* Works well for small tasks

**Cons:**

* Ignores word order → loses context
* Large vocabulary → sparse vectors
* Rare words can dominate or frequent words can overwhelm

## **Use Cases**

**When to use:**

* When you just need a simple representation of text.
* Useful in small datasets or when word order/context doesn’t matter.

**Examples:**

1. **Spam Detection:**

   * Classify emails as spam or not spam.
   * Words like “win”, “prize”, “free” → high counts in spam emails.
   * BoW vectorizes emails for ML models like Naive Bayes.

2. **Sentiment Analysis (Simple Version):**

   * Count positive and negative words to classify text as positive/negative.
   * Example: “I love this movie” → BoW vector shows high count for “love”.

3. **Document Classification:**

   * Categorize news articles (Sports, Politics, Tech) using word occurrence.

**Limitation:**

* Ignores word order → “not good” may be wrongly interpreted as positive.

---

## **2. TF-IDF (Term Frequency – Inverse Document Frequency)**

**Definition:**
TF-IDF improves on Bag of Words by **weighting words**. It gives higher importance to words that appear often in a document but **less often across the corpus**. This helps to identify words that are more “informative” about the document.

**Components:**

1. **TF (Term Frequency):** How often a term appears in a document.
2. **IDF (Inverse Document Frequency):** How rare a word is across all documents.
3. **TF-IDF Score:**


**Example:**

Doc1: "I love AI"

Doc2: "AI is amazing"

* "AI" appears in both → IDF lower
* "love" appears only in Doc1 → IDF higher

So TF-IDF gives more weight to **"love"** in Doc1 than to **"AI"**.

**Pros:**

* Highlights important words
* Reduces the impact of common words like “is”, “the”, etc.

**Cons:**

* Still ignores word order/context
* Rare words get high scores even if not meaningful

## **Use Cases**

**When to use:**

* When you want to **identify important keywords** in documents.
* Useful in information retrieval, search engines, and recommendation systems.

**Examples:**

1. **Search Engines:**

   * Query: “AI trends 2025”
   * TF-IDF scores rank documents with terms that are important and rare, giving relevant results first.

2. **Keyword Extraction:**

   * Automatically find important words in research papers or news articles.
   * Example: Extract keywords from an academic paper for indexing.

3. **Document Similarity / Clustering:**

   * Compare documents based on weighted word vectors.
   * Cluster similar news articles or reviews using cosine similarity.

**Limitation:**

* Still ignores word order/context.
* Doesn’t consider synonyms (e.g., “car” and “automobile” treated differently).
---

## **3. BM25 (Best Matching 25)**

**Definition:**
BM25 is an **advanced ranking function** often used in search engines. It’s based on TF-IDF but adds **saturation** and **document length normalization**, making it much better for retrieving relevant documents.

**How it works:**

1. Similar to TF-IDF, BM25 considers term frequency and rarity.
2. Adds **document length normalization** → longer documents don’t get unfairly high scores.
3. Uses a **saturation function** → term frequency doesn’t increase linearly; after some point, more repetitions don’t matter as much.

**Pros:**

* Widely used in search engines (Elasticsearch, Lucene)
* Handles long and short documents better
* Considers term saturation

**Cons:**

* More complex than TF-IDF
* Needs parameter tuning (k, b)

## **Use Cases**

**When to use:**

* When you need a **search/retrieval system** that ranks documents by relevance.
* Very common in modern search engines.

**Examples:**

1. **Search Engines (Google, Elasticsearch, Lucene):**

   * User query → rank documents by BM25 score.
   * Handles different document lengths and repetitive terms better than TF-IDF.

2. **Question Answering Systems:**

   * Retrieve relevant passages from a large document corpus.
   * Example: “Who is the CEO of Tesla?” → BM25 finds the most relevant paragraph.

3. **Recommendation Systems (Textual):**

   * Recommend articles, blogs, or products based on user query or content similarity.

**Limitation:**

* More computationally heavy than BoW or TF-IDF.
* Doesn’t capture semantics; “AI” and “Artificial Intelligence” are still separate terms.

---

### 🔹 **Summary of Use Cases**

| Method | Typical Use Case                                        |
| ------ | ------------------------------------------------------- |
| BoW    | Spam detection, sentiment analysis, text classification |
| TF-IDF | Search ranking, keyword extraction, document similarity |
| BM25   | Modern search engines, QA systems, content retrieval    |

---

## **1. Bag of Words (BoW) in Python**

We can use `CountVectorizer` from **scikit-learn**.

```python
from sklearn.feature_extraction.text import CountVectorizer

# Sample documents
docs = [
    "I love AI",
    "AI is amazing"
]

# Create BoW vectorizer
vectorizer = CountVectorizer()
X = vectorizer.fit_transform(docs)

# Vocabulary
print("Vocabulary:", vectorizer.get_feature_names_out())

# BoW Vectors
print("BoW Vectors:\n", X.toarray())
```

**Output:**

```
Vocabulary: ['ai', 'amazing', 'is', 'love']
BoW Vectors:
[[1 0 0 1]
 [1 1 1 0]]
```

---

## **2. TF-IDF in Python**

We can use `TfidfVectorizer` from **scikit-learn**.

```python
from sklearn.feature_extraction.text import TfidfVectorizer

# Sample documents
docs = [
    "I love AI",
    "AI is amazing"
]

# Create TF-IDF vectorizer
tfidf_vectorizer = TfidfVectorizer()
X = tfidf_vectorizer.fit_transform(docs)

# Vocabulary
print("Vocabulary:", tfidf_vectorizer.get_feature_names_out())

# TF-IDF Vectors
print("TF-IDF Vectors:\n", X.toarray())
```

**Output:**

```
Vocabulary: ['ai', 'amazing', 'is', 'love']
TF-IDF Vectors:
[[0.57973867 0.         0.         0.81480247]
 [0.45198512 0.6316672  0.6316672  0.        ]]
```

✅ Notice that "love" in Doc1 gets higher weight because it is rarer.

---

## **3. BM25 in Python**

Python doesn’t have BM25 in scikit-learn, but we can use **rank_bm25** library.

```python
# Install: pip install rank_bm25
from rank_bm25 import BM25Okapi

# Sample documents (tokenized)
docs = [
    ["i", "love", "ai"],
    ["ai", "is", "amazing"]
]

# Create BM25 model
bm25 = BM25Okapi(docs)

# Query (tokenized)
query = ["ai"]

# Get scores
scores = bm25.get_scores(query)
print("BM25 Scores:", scores)
```

**Output:**

```
BM25 Scores: [0.28768207 0.28768207]
```

✅ You can rank documents by `scores` to find the most relevant ones for a query.

---

### **Summary**

| Method | Library / Tool | Key Function    |
| ------ | -------------- | --------------- |
| BoW    | scikit-learn   | CountVectorizer |
| TF-IDF | scikit-learn   | TfidfVectorizer |
| BM25   | rank_bm25      | BM25Okapi       |

---

## **What are N-grams?**

**Definition:**
An n-gram is a **contiguous sequence of `n` items** from a given text. In NLP, the items are usually **words** (sometimes characters).

* **Unigram** → 1 word
* **Bigram** → 2 consecutive words
* **Trigram** → 3 consecutive words
* **n-gram** → n consecutive words

N-grams are used to **capture context** and **word sequences**, unlike Bag of Words which ignores order.

---

## **Why use N-grams?**

* Capture phrases instead of individual words.
  Example: “New York” → treated as one bigram.
* Improve text representation for NLP tasks: sentiment analysis, language modeling, text classification.
* Helps in predictive text or autocomplete systems.

---

## **Examples**

Text: `"I love AI and machine learning"`

* **Unigrams (1-word):**
  `["I", "love", "AI", "and", "machine", "learning"]`

* **Bigrams (2-word sequences):**
  `["I love", "love AI", "AI and", "and machine", "machine learning"]`

* **Trigrams (3-word sequences):**
  `["I love AI", "love AI and", "AI and machine", "and machine learning"]`

---

## **Python Implementation**

### Using **scikit-learn**:

```python
from sklearn.feature_extraction.text import CountVectorizer

text = ["I love AI and machine learning"]

# Unigrams
vectorizer_uni = CountVectorizer(ngram_range=(1,1))
print("Unigrams:", vectorizer_uni.fit(text).get_feature_names_out())

# Bigrams
vectorizer_bi = CountVectorizer(ngram_range=(2,2))
print("Bigrams:", vectorizer_bi.fit(text).get_feature_names_out())

# Trigrams
vectorizer_tri = CountVectorizer(ngram_range=(3,3))
print("Trigrams:", vectorizer_tri.fit(text).get_feature_names_out())
```

**Output:**

```
Unigrams: ['ai', 'and', 'learning', 'love', 'machine']
Bigrams: ['ai and', 'and machine', 'love ai', 'machine learning']
Trigrams: ['love ai and', 'ai and machine', 'and machine learning']
```

---

## **Use Cases of N-grams**

1. **Text Classification:**

   * Bigrams or trigrams can capture phrases like “not good” → better sentiment analysis.

2. **Language Modeling:**

   * Predict the next word based on previous words using bigrams/trigrams.

3. **Search Engines / Autocomplete:**

   * Suggest next words or queries using common n-grams.

4. **Spell Correction:**

   * Identify likely sequences of words to detect typos.

---

### ✅ **Summary Table**

| N-gram Type | Example         | Use Case                    |
| ----------- | --------------- | --------------------------- |
| Unigram     | I, love, AI     | Simple BoW, basic features  |
| Bigram      | I love, love AI | Phrases, context, sentiment |
| Trigram     | I love AI       | Context, predictive text    |

---

## **What is Named Entity Recognition (NER)?**

**Definition:**
Named Entity Recognition (NER) is an NLP task that **identifies and classifies “named entities” in text** into predefined categories.

* Named entities are typically **real-world objects** like people, organizations, locations, dates, numbers, etc.
* NER helps convert **unstructured text** into **structured information**.

---

## **Common Named Entity Categories**

| Entity Type          | Examples                          |
| -------------------- | --------------------------------- |
| Person (PER)         | "Elon Musk", "Albert Einstein"    |
| Organization (ORG)   | "Google", "NASA", "UNICEF"        |
| Location (LOC)       | "Paris", "India", "Mount Everest" |
| Date (DATE)          | "10th December 2025", "yesterday" |
| Time (TIME)          | "10:30 AM", "midnight"            |
| Money (MONEY)        | "$1000", "€50"                    |
| Percent (PERCENT)    | "50%", "three quarters"           |
| Miscellaneous (MISC) | Book names, events, products      |

---

## **Why NER is Useful**

1. **Information Extraction:**

   * Automatically extract entities from news, research papers, or social media.
   * Example: Extract all companies mentioned in a news article.

2. **Question Answering:**

   * Helps identify entities in the question and search relevant answers.
   * Example: “Who is the CEO of Tesla?” → NER identifies “Tesla” as an organization.

3. **Chatbots and Assistants:**

   * Detect entities in user input to provide accurate responses.
   * Example: Booking bots: "Book a flight to Paris on 10th December" → NER identifies "Paris" (LOC) and "10th December" (DATE).

4. **Text Summarization & Analytics:**

   * Identify key people, places, and organizations in documents.

---

## **Python Implementation (NER)**

We can use **spaCy**, a popular NLP library.

```python
# Install spaCy: pip install spacy
# Download English model: python -m spacy download en_core_web_sm

import spacy

# Load English model
nlp = spacy.load("en_core_web_sm")

# Sample text
text = "Elon Musk is the CEO of SpaceX and he lives in California."

# Process text
doc = nlp(text)

# Extract named entities
for ent in doc.ents:
    print(ent.text, "-", ent.label_)
```

**Output:**

```
Elon Musk - PERSON
SpaceX - ORG
California - GPE
```

✅ Explanation:

* `PERSON` → individual person
* `ORG` → organization
* `GPE` → geopolitical entity (city, country, state)

---

## **Key Points**

* NER is **supervised** in most models → trained on labeled datasets like CoNLL-2003.
* Pre-trained models (like spaCy, Hugging Face transformers) make it easy to use.
* NER can be **domain-specific**: finance, medicine, legal, etc.

  * Example: In medical texts, entities could be diseases, drugs, treatments.

---