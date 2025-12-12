# 🔍 **What is Fine-Tuning?**

LLMs like GPT, Llama, etc., are trained on trillions of tokens (general internet text).
But your application may require knowledge or behavior the base model doesn’t naturally have.

Fine-tuning =
✔️ Continue training the model
✔️ On your own curated dataset
✔️ To make it **specialized** for your domain or task

It’s like giving the model a new "skillset" on top of its general knowledge.

---

# 🧠 **Why Do We Need Fine-Tuning?**

## 1️⃣ **To Teach Domain-Specific Knowledge**

Base LLMs might not know:

* Internal company processes
* Medical/financial/legal terminology
* Product manuals
* Your custom APIs

Fine-tuning trains the model on this domain + improves performance.

**Example:**
Train on your company documents → model talks exactly like your organization.

---

## 2️⃣ **To Achieve Consistent Behavior**

Prompting alone can be inconsistent.
Fine-tuning **locks in** the behavior.

Examples:

* Always respond in a specific format
* Write in a specific tone (formal/concise)
* Follow strict rules (like JSON outputs)

---

## 3️⃣ **To Improve Performance on a Specific Task**

Tasks where fine-tuning helps:

* Classification
* Long-form question answering
* Summarization on special data
* Code generation for specific frameworks
* Chatbots for a specific product

---

## 4️⃣ **To Reduce Prompt Size (Cheaper + Faster)**

Without fine-tuning → you need a huge prompt telling the model what to do.

With fine-tuning → instructions are "baked into" the model.

Saves costs on:

* Tokens
* Inference time
* Context window usage

---

## 5️⃣ **To Personalize the Model**

Fine-tuning can make the LLM follow:

* Your brand style
* Your tone
* Your workflows

Example:
Customer support bot fine-tuned on past support tickets.

---

# 🎯 **Fine-Tuning vs Prompting vs RAG**

| Method                                   | When to Use                                                       |
| ---------------------------------------- | ----------------------------------------------------------------- |
| **Prompting**                            | Quick tasks, no training needed                                   |
| **RAG (Retrieval Augmented Generation)** | When knowledge changes frequently; avoid training                 |
| **Fine-tuning**                          | When you need the model to *learn patterns* or *behaviors* deeply |

---

# 📝 **Summary in 1 Line**

Fine-tuning teaches an LLM your exact task or domain so it becomes more accurate, consistent, and specialized than a general model.

---

# ✅ **1. What is LoRA? (Low-Rank Adaptation)**

**LoRA is a parameter-efficient fine-tuning (PEFT) method** used to train large language models (LLMs) without updating all their weights.

### ⭐ Why LoRA?

Modern LLMs have **billions of parameters** → full fine-tuning is:

* Expensive (GPU memory + compute)
* Slow
* Hard to store multiple fine-tuned models

LoRA solves this by **freezing the original model weights** and learning **small low-rank matrices** instead.

---

## 🔍 **How LoRA works**

In a transformer, most of the parameters are in linear layers (like Query, Key, Value projections).

A normal linear layer does:

```
y = W * x
```

LoRA injects a small learnable update:

```
W' = W + ΔW
```

But instead of learning a big ΔW (same size as W), LoRA decomposes it into **two small matrices**:

```
ΔW = B * A
```

Where:

* **A** has shape (r × input_dim)
* **B** has shape (output_dim × r)
* **r** is very small (rank) → usually 4, 16 etc.

### 🧠 Interpretation:

* You freeze **W** (big matrix)
* You train small matrices **A** and **B**
* You get huge memory savings (10x–100x)

### ✔ Example

Instead of updating a **4096 × 4096 matrix**, LoRA adds:

* A: (4 × 4096)
* B: (4096 × 4)

That’s **0.2%** of the parameters!

---

## 🔋 Benefits of LoRA

* **Huge memory savings**
* **Fast training**
* **You can stack multiple LoRA adapters** on the same base model
* **Works with quantized models too** → (QLoRA)

---

# ✅ **2. What is QLoRA? (Quantized LoRA)**

QLoRA = **Quantization + LoRA**

It was introduced to fine-tune **very large 33B–70B+ models on a single GPU (24GB)**.

### ⭐ Key idea

Fine-tuning huge models becomes possible if:

1. **Base model weights are quantized to 4-bit** (QLoRA uses NF4 quantization)
2. **LoRA is applied on top** (so only small adapters are trained)

---

## 🔍 **How QLoRA works internally**

### **Step 1 — 4-bit Quantization (NF4)**

Weights W are stored in **4-bit NormalFloat4 format**, reducing memory by **75%**.

### **Step 2 — Freeze the quantized weights**

Just like LoRA, the original model is NOT updated.

### **Step 3 — Compute in 16-bit using double quantization**

At inference, quantized weights are temporarily dequantized into FP16 for operations — but memory stays low.

This uses:

* **4-bit NF4 weights**
* **8-bit quantization constants**
* **16-bit computation**

### **Step 4 — Attach LoRA adapters (A and B matrices)**

Trainable LoRA matrices use FP16/BF16 precision.

So QLoRA =

```
4-bit base model (frozen)
+ FP16 LoRA adapters (trainable)
```

---

# 🚀 Why QLoRA is even better than LoRA?

| Feature                    | LoRA             | QLoRA                                    |
| -------------------------- | ---------------- | ---------------------------------------- |
| Base model precision       | FP16/FP32        | **4-bit NF4**                            |
| Adapter training           | Yes              | Yes                                      |
| GPU usage                  | Low              | **Very Low**                             |
| Can fine-tune huge models? | Up to 13B easily | **Up to 70B on a single GPU**            |
| Training quality           | Good             | **Almost identical** to full fine-tuning |

QLoRA gives:

* **Saving of 60–70% GPU memory**
* Same performance as LoRA
* Almost no loss in accuracy compared to full fine-tuning

---

# 🧠 Summary (Interview-friendly)

### **LoRA**

* Parameter-efficient fine-tuning.
* Injects low-rank matrices into transformer layers.
* Only trains **<1%** of parameters.
* Saves memory and compute.

### **QLoRA**

* Quantized LoRA.
* Loads the base model in **4-bit** precision.
* Only trains LoRA adapters.
* Enables fine-tuning **70B LLMs on a single GPU**.

### Simple intuition:

```
LoRA = Train tiny rank-r matrices instead of huge weight matrices.
QLoRA = LoRA + 4-bit quantization → ultra low-memory training.
```

---
