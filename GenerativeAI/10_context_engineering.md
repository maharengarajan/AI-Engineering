## 1. What is Context Engineering?

**Context engineering** is the deliberate design, selection, compression, and structuring of *everything you give a generative model as input* so it produces the best possible output.

> If prompt engineering is *what you ask*,
> **context engineering is *what the model knows at that moment*.**

This includes:

* System instructions
* User prompts
* Conversation history
* Retrieved documents (RAG)
* Tool outputs
* Memory / user profile
* Constraints, schemas, and examples

LLMs don’t “think” beyond their context window—**the context *is* their world**.

---

## 2. Why Context Engineering Matters

### Key reasons:

1. **LLMs are stateless**

   * They don’t remember past interactions unless you send them again
2. **Context window is limited**

   * You must choose *what matters most*
3. **More context ≠ better output**

   * Irrelevant or noisy context *hurts performance*
4. **Production systems live or die by consistency**

   * Same input → same quality output

In practice:

> Bad context beats a good model
> Good context can rescue a weaker model

---

## 3. Context vs Prompt Engineering

| Aspect  | Prompt Engineering   | Context Engineering                                  |
| ------- | -------------------- | ---------------------------------------------------- |
| Scope   | Single instruction   | Entire input space                                   |
| Focus   | Wording, phrasing    | Selection, ordering, structure                       |
| Level   | Micro                | System-level                                         |
| Example | “Explain like I’m 5” | Supplying examples, policies, memory, retrieved docs |

Think of prompt engineering as **copywriting**,
and context engineering as **information architecture**.

---

## 4. What Makes Up “Context”?

A typical production prompt stack looks like this:

```
[System Instructions]
[Developer Rules / Policies]
[User Profile / Memory]
[Conversation Summary]
[Retrieved Knowledge]
[User Question]
```

### 1. System Instructions

* Role, tone, constraints
* Safety and formatting rules

Example:

```
You are a senior backend architect.
Answer concisely with diagrams in text.
```

---

### 2. Conversation History (or Summary)

* Past messages or compressed state
* Often summarized to save tokens

Bad:

```
Full chat history (10k tokens)
```

Good:

```
Conversation summary:
- User is designing a payment system
- Chose event-driven architecture
```

---

### 3. Retrieved Context (RAG)

Dynamic knowledge pulled at runtime:

* Docs
* APIs
* DB records
* Logs

Example:

```
Relevant document:
Section 3.2 – Rate limiting policy (updated Jan 2026)
```

This is where **context engineering meets information retrieval**.

---

### 4. Examples (Few-shot)

Examples shape behavior more than rules.

```
Input: "Summarize this error"
Output: "Root cause + Fix + Prevention"
```

The model will imitate structure, tone, and depth.

---

### 5. Constraints & Schemas

Used heavily in structured output:

```
Return JSON with:
- root_cause: string
- severity: enum(low|medium|high)
```

---

## 5. Core Context Engineering Techniques

### 1. Context Selection (What to include)

Ask:

* Is this directly relevant to the task?
* Does it change the output?
* Can it be inferred?

If not → drop it.

---

### 2. Context Ordering (Where to place it)

Order matters.

**Effective order:**

1. Role & rules
2. Constraints
3. Examples
4. Retrieved knowledge
5. User query

LLMs give more weight to **recent and explicit** instructions.

---

### 3. Context Compression

Since tokens are expensive:

* Summarize conversations
* Chunk and retrieve top-k docs
* Remove redundant phrasing
* Use bullet points instead of prose

Example:

```
Instead of:
"The user has previously mentioned..."

Use:
"User preference: concise, code-first"
```

---

### 4. Context Grounding

Prevent hallucinations by anchoring the model:

```
Answer ONLY using the provided documents.
If unsure, say "Not found in context."
```

Critical for:

* Enterprise search
* Legal / medical systems
* Financial analytics

---

### 5. Dynamic Context Assembly

Context is often built *at runtime*:

```python
context = []
context += system_rules
context += retrieve_docs(query)
context += user_memory
context += user_prompt
```

This is the backbone of **agentic systems**.

---

## 6. Context Engineering in Real Systems

### Example 1: Chatbot with Memory

* Short-term: conversation summary
* Long-term: user profile
* Task-specific: retrieved docs

### Example 2: Coding Assistant

* Repo structure
* Relevant files
* Language style guide
* Current diff

### Example 3: Customer Support AI

* Ticket history
* SLA rules
* Product documentation
* User sentiment

---

## 7. Common Mistakes

❌ Dumping entire documents
❌ Repeating the same rules everywhere
❌ Mixing irrelevant context
❌ No clear source separation
❌ Relying on model “intuition”

---

## 8. Mental Model (Important)

Think of the LLM as:

> **A brilliant intern with amnesia and a tiny desk**

* It can reason deeply
* But only with what fits on the desk
* And it forgets everything when you leave

Your job is to **decide what goes on that desk**.

---

## 9. How Context Engineering Fits in HLD (Since You’re Learning HLD)

In system design, context engineering lives at:

* **Input orchestration layer**
* **Prompt service**
* **Agent runtime**
* **RAG pipelines**

It’s a **first-class design concern**, not an afterthought.

---

## 10. One-Line Summary

> **Context engineering is the art and science of deciding what an LLM sees, in what form, and in what order—so it behaves like a reliable system, not a lucky demo.**