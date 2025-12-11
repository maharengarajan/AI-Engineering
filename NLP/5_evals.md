# 🌟 **What is BERT?**

**BERT = Bidirectional Encoder Representations from Transformers**

It is a **pretrained language model** developed by Google that understands text **in both directions** (left → right and right → left) using the **Transformer Encoder** architecture.

Before BERT, most NLP models read sentences **in one direction** (like left→right), which meant they didn’t fully understand context.
**BERT changed the game** by reading the *whole sentence at once* → which gives it deep contextual understanding.

---

# 🧠 **Why is BERT powerful?**

Because it is:

### ✔ **Bidirectional**

It looks at *both previous and next words* simultaneously.
Example:

> "The **bank** is surrounded by water."

> "The **bank** approved my loan."

BERT uses both sides of the word **bank** to understand the meaning.

---

### ✔ **Pretrained on massive text**

BERT is trained on:

* Wikipedia (2.5B words)
* BooksCorpus (800M words)

It learns **grammar**, **meaning**, **relationships**, and **world knowledge** before you fine-tune it.

---

### ✔ **Fine-tuning ready**

Same model can be fine-tuned for:

* Text classification
* Named Entity Recognition
* Q&A
* Sentiment Analysis
* Summarization
* Search relevance…

You just add task-specific layers and train for a few minutes.

---

# 🔧 **How does BERT work internally?**

BERT uses the **Transformer Encoder** stack (not the decoder).

### Key components:

* **Self-attention** (understand relationships between any two words)
* **Positional embeddings** (order of words)
* **Deep stacked layers** (12 or 24)

---

# 📚 **How BERT is pretrained**

BERT uses **two training objectives:**

---

### 1️⃣ **Masked Language Modeling (MLM)**

Randomly masks 15% of the words.

Example:

> “The cat sat on the **[MASK]**.”

Model predicts: **mat**

This forces bidirectional understanding.

---

### 2️⃣ **Next Sentence Prediction (NSP)**

Trains BERT to understand sentence relationships.

Example:

* Sentence B truly follows A → **IsNext**
* Sentence B is random → **NotNext**

Used for Q&A, search ranking, etc.

---

# 🧬 **BERT Variants**

* **BERT-Base** → 12 layers, 110M params
* **BERT-Large** → 24 layers, 340M params
* **DistilBERT** → Small, fast version
* **RoBERTa** → Improved BERT without NSP
* **ALBERT** → Smaller memory footprint
* **BioBERT, ClinicalBERT** → Domain-specific

---

# 📦 **Simple Python Example Using BERT (Text Classification)**

```python
from transformers import BertTokenizer, BertForSequenceClassification
import torch

# Load model and tokenizer
tokenizer = BertTokenizer.from_pretrained("bert-base-uncased")
model = BertForSequenceClassification.from_pretrained("bert-base-uncased")

# Input text
text = "BERT is a powerful transformer model"

# Tokenize
inputs = tokenizer(text, return_tensors="pt", padding=True, truncation=True)

# Forward pass
outputs = model(**inputs)

# Logits → prediction
logits = outputs.logits
pred = torch.argmax(logits, dim=1)

print(pred)
```

---

# 🏆 **Why BERT is important**

Before BERT → NLP models were mostly one-directional and less context-aware.
After BERT → Almost all modern models (RoBERTa, ALBERT, DistilBERT, T5, GPT) followed its architecture or ideas.

BERT brought:

* Better contextual understanding
* State-of-the-art performance on 11 NLP benchmarks
* Practical fine-tuning for real-world tasks

---
# ✅ Fine-tuning **BERT for Named Entity Recognition (NER)** 

---

# ✅ **1. Understanding NER with BERT**

NER = labeling each token with a tag:

Example:

| Word   | Label |
| ------ | ----- |
| John   | B-PER |
| works  | O     |
| at     | O     |
| Google | B-ORG |

So it becomes a **token classification task**.

---

# ✅ **2. Format your custom dataset**

The easiest format is **CoNLL format** (token + label per line):

```
John    B-PER
works   O
at      O
Google  B-ORG
.       O

I       O
live    O
in      O
India   B-LOC
```

Each sentence separated by a blank line.

Let your dataset consist of:

```
train.txt
valid.txt
test.txt
```

---

# ✅ **3. Install dependencies**

```bash
pip install transformers datasets seqeval
```

---

# ✅ **4. Load dataset using HuggingFace Datasets**

```python
from datasets import load_dataset

dataset = load_dataset("text", data_files={
    "train": "train.txt",
    "validation": "valid.txt",
    "test": "test.txt"
})
```

Now we must **parse** the CoNLL format into tokens + labels.

---

# ✅ **5. Preprocess and convert CoNLL to structured format**

```python
def parse_conll(batch):
    sentences = []
    labels = []

    for text in batch["text"]:
        tokens = []
        tags = []
        for line in text.split("\n"):
            if not line.strip():
                if tokens:
                    sentences.append(tokens)
                    labels.append(tags)
                    tokens, tags = [], []
            else:
                token, tag = line.split()
                tokens.append(token)
                tags.append(tag)
    return {"tokens": sentences, "ner_tags": labels}

parsed = dataset.map(parse_conll, batched=True, remove_columns=["text"])
```

---

# ✅ **6. Encode tokens using BERT tokenizer**

We convert labels into IDs and align them with BERT tokens (important because BERT may split words).

```python
from transformers import BertTokenizerFast

tokenizer = BertTokenizerFast.from_pretrained("bert-base-uncased")

label_list = list(set(tag for sent in parsed["train"]["ner_tags"] for tag in sent))
label2id = {l:i for i,l in enumerate(label_list)}
id2label = {i:l for l,i in label2id.items()}
```

### Tokenize and align labels

```python
def tokenize_and_align_labels(examples):
    tokenized = tokenizer(examples["tokens"], truncation=True, is_split_into_words=True)

    labels = []
    for i, label_seq in enumerate(examples["ner_tags"]):
        word_ids = tokenized.word_ids(batch_index=i)
        aligned = []
        prev_word = None

        for wid in word_ids:
            if wid is None:
                aligned.append(-100)
            elif wid != prev_word:  # first subtoken
                aligned.append(label2id[label_seq[wid]])
            else:  # subsequent subtokens
                aligned.append(-100)
            prev_word = wid

        labels.append(aligned)

    tokenized["labels"] = labels
    return tokenized

encoded = parsed.map(tokenize_and_align_labels, batched=True)
```

---

# ✅ **7. Load BERT token-classification model**

```python
from transformers import BertForTokenClassification

model = BertForTokenClassification.from_pretrained(
    "bert-base-uncased",
    num_labels=len(label_list),
    id2label=id2label,
    label2id=label2id
)
```

---

# ✅ **8. Training setup**

```python
from transformers import TrainingArguments, Trainer

args = TrainingArguments(
    "bert-ner",
    evaluation_strategy="epoch",
    save_strategy="epoch",
    learning_rate=5e-5,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=16,
    num_train_epochs=5,
    weight_decay=0.01,
)
```

---

# ✅ **9. Metrics (F1-score using seqeval)**

```python
import numpy as np
from seqeval.metrics import classification_report, f1_score

def compute_metrics(pred):
    logits, labels = pred
    preds = np.argmax(logits, axis=-1)

    pred_list = []
    true_list = []

    for p, l in zip(preds, labels):
        temp_pred = []
        temp_true = []
        for x, y in zip(p, l):
            if y != -100:
                temp_true.append(id2label[y])
                temp_pred.append(id2label[x])
        pred_list.append(temp_pred)
        true_list.append(temp_true)

    return {"f1": f1_score(true_list, pred_list)}
```

---

# ✅ **10. Train the model**

```python
trainer = Trainer(
    model=model,
    args=args,
    train_dataset=encoded["train"],
    eval_dataset=encoded["validation"],
    tokenizer=tokenizer,
    compute_metrics=compute_metrics,
)

trainer.train()
```

---

# ✅ **11. Evaluate**

```python
trainer.evaluate()
```

---

# ✅ **12. Run inference on new sentences**

```python
sentence = "Ramesh works at Microsoft in Hyderabad."

inputs = tokenizer(sentence, return_tensors="pt")
outputs = model(**inputs)

pred_ids = outputs.logits.argmax(-1).squeeze().tolist()
tokens = tokenizer.convert_ids_to_tokens(inputs["input_ids"][0])

for token, pid in zip(tokens, pred_ids):
    if token not in ["[CLS]", "[SEP]"]:
        print(token, "→", id2label[pid])
```

---

# 🎉 **DONE! You have fine-tuned BERT for NER on your custom dataset.**

---