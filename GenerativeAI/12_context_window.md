# 1️⃣ What is a *context window*

👉 **Context window = the maximum amount of text the model can “see” at one time**

That includes:

* what **you send** (system + user messages)
* what the **model has already replied**
* what the **model is about to generate**

Once the window is full, the model **cannot see anything beyond it**.

Think of it like a **whiteboard**:

* You keep writing on it
* When it’s full, you must **erase something** before adding more

---

# 2️⃣ What counts toward the context window?

Everything below counts as tokens:

```
System prompt
+ Conversation history
+ Documents you paste
+ Tool results
+ Model’s previous replies
+ Model’s new reply (being generated)
-----------------------------------
= Total tokens in context window
```

📌 **Important:**
The model must *reserve space* for its answer **inside the same window**.

---

# 3️⃣ Example: 200K context window (Claude Sonnet 4.5 standard)

Let’s say the model has:

* Context window: **200,000 tokens**

You send:

* 150K tokens of documents
* 20K tokens of chat history

So far:

```
150K + 20K = 170K used
```

Now the model wants to answer.

That answer must **fit in the remaining space**:

```
200K − 170K = 30K max output
```

If you ask for a 50K-token answer → ❌ **error**
Because the model has nowhere to put it.

---

# 4️⃣ Why “output limit” exists

Even if the window is 200K, models often have a **practical output cap**.

For example:

* Claude Sonnet 4.5 may support:

  * 200K total context
  * ~128K max output in one response

So this is valid:

```
Input: 70K
Output: 128K
Total: 198K
```

But this is NOT:

```
Input: 120K
Output: 128K
Total: 248K ❌
```

---

# 5️⃣ 1M context window (Extended / Beta)

This is where confusion explodes 😄

### ❌ WRONG interpretation

> “Claude remembers 1M tokens forever”

### ✅ CORRECT interpretation

> “Claude can see **up to 1M tokens in a single request**”

Example:

* Entire codebase = 900K tokens
* Prompt = 20K
* Output = 50K

Total:

```
900K + 20K + 50K = 970K ✔️
```

But next request?

* If you don’t resend that content (or cache it), **it’s gone**

---

# 6️⃣ Context window ≠ Memory

This is **very important**:

| Concept        | What it means                      |
| -------------- | ---------------------------------- |
| Context window | Short-term working memory          |
| Chat history   | Just text you keep resending       |
| Model memory   | ❌ Claude has none across API calls |
| RAG / DB       | External long-term memory          |
| Prompt caching | Cost optimization, not memory      |

Claude **does not remember previous calls** unless you send them again.

---

# 7️⃣ What happens when you exceed the context window?

Depends on the platform:

### Claude API

* ❌ Request fails with an error
* Nothing is silently dropped

### Chat UI (Claude.ai)

* Old messages may be summarized or truncated automatically
* You usually don’t see it happening

---

# 8️⃣ Why large context windows matter (practical reasons)

Large context helps with:

✔ Whole codebase analysis
✔ Long research papers
✔ Multi-document reasoning
✔ Agents running long workflows
✔ Fewer hallucinations (because model can “see” more facts)

But:

⚠️ Larger context = more cost
⚠️ Slower inference
⚠️ Not a substitute for databases or search

---

# 9️⃣ Cost is based on **tokens inside the window**

You pay for:

* All input tokens
* All output tokens

Example (Sonnet 4.5 standard pricing):

```
180K input tokens
+ 10K output tokens
= 190K tokens billed
```

Even if the model “just reads” something — you still pay.

---

# 🔟 Mental model that usually makes it click

Imagine this:

> Claude is **stateless**
> Each request is like handing it a **stack of papers**
> The stack cannot exceed **N tokens**
> Claude reads the entire stack **every time**

If the stack is too big → ❌ rejected

---

# 11️⃣ Common misconceptions (quick fixes)

❌ “Bigger context = smarter model”
✅ Bigger context = **more text visible**

❌ “Claude remembers past chats”
✅ You keep resending them

❌ “1M tokens means unlimited”
✅ Hard cap per request

❌ “Context window = output size”
✅ Output is **part of** the window

---

# 12️⃣ One-line definition (exam-ready)

> **Context window is the maximum number of tokens (input + output) a model can process in a single request.**

---
