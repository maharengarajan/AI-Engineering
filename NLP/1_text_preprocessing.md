# 🔥 NLP Preprocessing Techniques (Core Concepts)

Preprocessing helps convert raw text into a clean, structured form so models can understand it better.

---

# 1️⃣ **Tokenization**

**Breaking text into smaller units** → words, subwords, or sentences.

Example:
`"I love Natural Language Processing"` →
`["I", "love", "Natural", "Language", "Processing"]`

Types:

* **Word tokenization**
* **Sentence tokenization**
* **Subword tokenization** (BPE, WordPiece → used in BERT, GPT etc.)

---

# 2️⃣ **Lowercasing (optional)**

Convert text to lowercase.
`"Apple"` → `"apple"`

✔️ Good for classical ML
❌ Not used in some transformer models (e.g., BERT has cased & uncased versions)

---

# 3️⃣ **Stopword Removal**

Stopwords are **common words that do not add much meaning**.

Examples::
`is`, `am`, `the`, `and`, `was`, `of`

Sentence:
`"I am learning NLP"` → remove stopwords → `"learning NLP"`

Why remove?

* Reduces noise
* Reduces dimensionality in Bag-of-Words, TF-IDF

But **not always recommended** for transformers (they understand context).

---

# 4️⃣ **Stemming**

Stemming chops words to their base/root form by **rule-based truncation**.

Examples:

* `"playing"` → `play`
* `"studies"` → `studi`
* `"better"` → `better` (irregular words fail)

Popular stemmers:

* Porter Stemmer
* Snowball Stemmer
* Lancaster Stemmer

✔️ Simple, fast
❌ Not linguistically correct (may distort words)

---

# 5️⃣ **Lemmatization**

Lemmatization converts words to their **dictionary root form (lemma)** using grammar rules.

Examples:

* `"studies"` → `study`
* `"better"` → `good`
* `"running"` → `run`

It uses:

* Part-of-speech (POS)
* WordNet dictionary

✔️ More accurate than stemming
❌ Slower

---

# 6️⃣ **Part-of-Speech (POS) Tagging**

Assigning grammatical roles:

Example:
`"Time flies like an arrow"`

* Time → noun
* flies → verb
* like → preposition

Useful for:

* Lemmatization
* Named Entity Recognition
* Parsing

---

# 7️⃣ **Removing Punctuation**

`"Hello!!! How are you?"` → `"Hello How are you"`

Useful for:

* Classical ML
* Sentiment analysis

Not always needed for transformers.

---

# 8️⃣ **Removing Numbers**

`"I bought 5 apples"` → `"I bought apples"`

Depends on the task.
(For finance, numbers are important → don’t remove)

---

# 9️⃣ **Normalizing Contractions**

Convert short forms to full forms:

* `don't` → `do not`
* `I'm` → `I am`

Improves clarity for models.

---

# 🔟 **Handling Emojis / Emoticons**

Emojis give emotional cues.
😊 → "smiling face"
😡 → "angry face"

Useful for sentiment analysis.

---

# 1️⃣1️⃣ **Text Normalization**

Includes:

* Replacing repeated characters → `"soooo good"` → `"so good"`
* Fixing spelling → `"teh"` → `"the"`

---

# 1️⃣2️⃣ **Vectorization (Feature Extraction)**

Converting text to numbers:

* **Bag of Words**
* **TF-IDF**
* **Word2Vec**
* **GloVe**
* **BERT embeddings**

---

# 🌟 Stemming vs Lemmatization (Very Important for Interviews)

| Feature  | Stemming            | Lemmatization           |
| -------- | ------------------- | ----------------------- |
| Output   | Root by chopping    | Dictionary root (lemma) |
| Grammar  | Ignores grammar     | Grammar-aware           |
| Speed    | Fast                | Slower                  |
| Accuracy | Low                 | High                    |
| Example  | studies → **studi** | studies → **study**     |

---

# ✔️ When to Use Which?

### **For classical ML models (TF-IDF, Bag-of-Words):**

* Stopwords removal → YES
* Stemming/Lemmatization → YES

### **For transformer models (BERT, GPT):**

* Stopwords removal → NO (they understand context)
* Stemming/Lemmatization → NO (destroys context)
* Tokenization → Done by model tokenizer (WordPiece, BPE)

---
