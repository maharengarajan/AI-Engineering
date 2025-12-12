# ✅ **1. BLEU Score (Bilingual Evaluation Understudy)**

**Used for:** Machine Translation, Text Generation
**Type:** Precision-based metric (how much of your generated text appears in the reference)

### **Idea**

BLEU measures **how similar the generated text is to a reference** by counting overlapping **n-grams** (like 1-gram, 2-gram).

### 🎯 Key Points

* Measures **precision** → “How much of what I generated is correct?”
* Penalty for **too short** output (brevity penalty)
* Range: **0 to 1** (or 0 to 100%)
* Higher = better

### 🔹 Example

Generated:
`I love eating apples`

Reference:
`I like eating apples`

BLEU will count overlap:

* “I”, “eating”, “apples” match
* “love” doesn’t match “like”

---

# ✅ **2. ROUGE Score (Recall-Oriented Understudy for Gisting Evaluation)**

**Used for:** Summarization, Similarity
**Type:** Recall-based metric (how much of the reference appears in your generated text)

### **Idea**

ROUGE checks **how much of the reference summary is captured by your model’s summary**.

### 🎯 Types

* **ROUGE-1** → unigram overlap
* **ROUGE-2** → bigram overlap
* **ROUGE-L** → longest common subsequence (sequence similarity)

### 🔹 Example

Reference summary contains important words.
ROUGE checks: *Does your model’s summary include them?*

### ✔ Why used in summarization?

Because human-written summary = “ground truth important info.”
We want to see if our summary contains the **important points** (recall).

---

# ✅ **3. Perplexity**

**Used for:** Language Models (LMs), text generation quality
**Type:** Probability-based metric

### **Idea**

Perplexity measures **how well the model predicts the next word**.

Lower perplexity = better language model.

### 🎯 Interpretation

If perplexity = 20
→ model is as “confused” as if it had to choose between 20 words.

### ✔ Why useful?

* Evaluates LLMs, GPT-style models
* Not dependent on reference text
* Based on model’s internal probability distribution

---

# ✅ **4. Accuracy / F1 Score**

**Used for:** Classification NLP tasks

* Text classification
* Sentiment analysis
* NER (Named Entity Recognition)
* POS tagging

### 🟦 NER example

Predicted:
`Apple` → ORG
Actual:
`Apple` → ORG
Good!

But if the span or label is off, metrics drop.

**F1 Score** combines

* Precision (what you predicted correctly)
* Recall (what you should have predicted)

---

# ✅ **5. METEOR Score**

**Used for:** Translation, Paraphrasing
**Better than BLEU** because:

* Includes **stemming**
* Includes **synonyms**
* Considers **word order**

### Example

Generated: “He purchased a car”
Reference: “He bought a car”

BLEU: low (bought ≠ purchased)
METEOR: high (purchased ≈ bought)

---

# ✅ **6. CIDEr Score**

**Used for:** Image Captioning
Measures consensus between generated captions and multiple human captions.

Focuses on important words using TF-IDF weighting.

---

# ✅ **7. BERTScore**

**Used for:** Translation, Summarization, Paraphrasing
**Semantic similarity metric**

Uses BERT embeddings → checks meaning, not just matching words.

### Example

Generated: "He went to the store"
Reference: "He visited the shop"

BLEU → low
BERTScore → high (because meaning matches)

---

# 📌 **Which Metric Should You Use?**

| Task Type                             | Best Metrics                         |
| ------------------------------------- | ------------------------------------ |
| **Machine Translation**               | BLEU, METEOR, BERTScore              |
| **Summarization**                     | ROUGE-1, ROUGE-2, ROUGE-L, BERTScore |
| **Language Models**                   | Perplexity                           |
| **Text Generation (Chat/LLM)**        | BERTScore, Human Evaluation          |
| **Image Captioning**                  | CIDEr, BLEU                          |
| **Classification (Sentiment, Topic)** | Accuracy, Precision, Recall, F1      |
| **NER**                               | F1 Score (strict or relaxed)         |

---

# 🔥 Quick Memory Trick

* **BLEU → Precision → Translation**
* **ROUGE → Recall → Summarization**
* **Perplexity → How confused is the LM?**
* **BERTScore → Semantic similarity**
* **F1 → For classification/NER (balanced metric)**

---
