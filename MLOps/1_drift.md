# 🚀 What Is Drift in MLOps?

In MLOps, **drift** refers to a change in real-world conditions compared to what the model saw during training.
When the world changes, the ML model’s assumptions no longer hold → **performance drops**.

Drift is a natural part of production ML systems. Detecting and handling drift is a key MLOps responsibility.

---

# 1️⃣ **Data Drift (Feature Drift)**

### 📌 Definition

Data drift happens when the **input data distribution changes** over time compared to training data.

### 💡 Example

You trained a fraud detection model on 2023 user behavior.
In 2025, users behave differently (new spending patterns, new payment methods).
The **distribution of input features changes**, so the model becomes less accurate.

### 🔍 Key Symptoms

* Mean/variance of features change
* New categories appear
* Missing values increase
* Changes in data collection systems
* Sensor calibration changes

### 🔧 Monitoring Techniques

* KS test, Chi-square
* Population Stability Index (PSI)
* KL divergence
* Feature histograms comparison

---

# 2️⃣ **Concept Drift**

### 📌 Definition

Concept drift occurs when the **relationship between inputs (X) and output (Y) changes**.
So—even if input data distribution is the same—the meaning of prediction changes.

### 💡 Example

A model predicts whether a transaction is fraud.
Fraudsters change their strategies → the **mapping from features → fraud probability changes**.

### 🧠 Simple View

* Data drift = change in **X**
* Concept drift = change in **f(X → Y)**

### 🔧 Monitoring Techniques

* Track accuracy/precision/recall
* Compare model predictions vs actual labels
* Drift detection methods (DDM, EDDM, ADWIN)

---

# 3️⃣ **Model Drift**

### 📌 Definition

Model drift is when the **model becomes outdated** because it no longer represents the current patterns — usually due to data drift or concept drift.

Think of it as:

> “The model is no longer valid for current real-world data.”

### 💡 Example

You trained a recommendation model on old user behavior.
As preferences change, the model’s embeddings become outdated → poor recommendations.

### 🔍 Why It Happens?

* Underlying data changes
* Business logic changes
* Competitors/new products change patterns
* Seasonality (festive sales, holidays)

### 🔧 Fix

* Retraining
* Online learning
* Scheduled model refresh

---

# 4️⃣ **Performance Drift (Accuracy Drift)**

### 📌 Definition

Performance drift means the **model’s evaluation metrics degrade over time**, e.g.:

* Lower accuracy
* Lower precision/recall
* Higher error rate
* More customer complaints
* Higher MSE/MAE for regression

### 💡 Example

Demand forecasting model:
Good accuracy for months → suddenly error increases because of a new marketing campaign or a seasonal shift.

### 🔍 Monitoring Techniques

* Monitor prediction-target error
* Compare fresh ground-truth labels with predictions
* Dashboard monitoring (e.g., MLFlow, Evidently AI)

---

# 🧠 How They Differ (Quick Summary Table)

| Drift Type            | What Changes?                | Why It Happens?                          | Example                                 |
| --------------------- | ---------------------------- | ---------------------------------------- | --------------------------------------- |
| **Data Drift**        | Input data (X) distribution  | New user behavior, changes in collection | Change in age distribution of customers |
| **Concept Drift**     | Relationship between X and Y | Real-world meaning changes               | Fraud tactics evolve                    |
| **Model Drift**       | Model becomes outdated       | Due to data or concept drift             | Old embeddings, stale parameters        |
| **Performance Drift** | Output metrics drop          | Any change affecting accuracy            | Accuracy drops from 90% → 70%           |

---

# 🏁 Final 30-second Interview Answer

> **Drift in MLOps** refers to changes over time that make the model less accurate in production.
> **Data drift** is when the input data distribution changes.
> **Concept drift** is when the relationship between inputs and output changes.
> **Model drift** is when the model becomes outdated because the world has changed.
> **Performance drift** is when model accuracy degrades in production due to any of the above drifts.
> Detecting and addressing drift is crucial for maintaining reliable ML systems.

---
