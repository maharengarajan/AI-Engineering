## 1️⃣ Iterable

### What is an Iterable?

An **iterable** is **any object that you can loop over** using a `for` loop.

👉 Technically:

* It implements the `__iter__()` method
* `__iter__()` returns an **iterator**

### Common Iterables

* `list`, `tuple`, `set`, `dict`
* `string`
* `range`

### Example

```python
nums = [1, 2, 3, 4]

for n in nums:
    print(n)
```

Here:

* `nums` is an **iterable**
* Python internally calls `iter(nums)`

### Check if object is iterable

```python
from collections.abc import Iterable

isinstance(nums, Iterable)   # True
```

---

## 2️⃣ Iterator

### What is an Iterator?

An **iterator** is an object that:

* Produces values **one at a time**
* Remembers its **current state**
* Implements:

  * `__iter__()`
  * `__next__()`

### Key Properties

* Can be traversed **only once**
* Raises `StopIteration` when exhausted

### Example

```python
nums = [1, 2, 3]

it = iter(nums)   # get iterator

print(next(it))  # 1
print(next(it))  # 2
print(next(it))  # 3
print(next(it))  # StopIteration error
```

### Iterator is also iterable

```python
iter(it) is it  # True
```

---

## 3️⃣ Generator

### What is a Generator?

A **generator** is a **special type of iterator** created using:

* A function with `yield`
* Or a generator expression

Generators:

* Produce values **lazily**
* Do **not store all values in memory**
* Automatically implement `__iter__()` and `__next__()`

---

### Generator Function Example

```python
def count_up(n):
    for i in range(1, n + 1):
        yield i

gen = count_up(3)

print(next(gen))  # 1
print(next(gen))  # 2
print(next(gen))  # 3
```

---

### Generator Expression

```python
gen = (x * x for x in range(5))

for val in gen:
    print(val)
```

Equivalent to:

```python
def square():
    for x in range(5):
        yield x * x
```

---

## 4️⃣ Key Differences (Very Important)

| Feature                 | Iterable   | Iterator | Generator      |
| ----------------------- | ---------- | -------- | -------------- |
| Can be looped           | ✅          | ✅        | ✅              |
| Has `__iter__()`        | ✅          | ✅        | ✅              |
| Has `__next__()`        | ❌          | ✅        | ✅              |
| Stores values in memory | Usually ✅  | ❌        | ❌              |
| One-time use            | ❌          | ✅        | ✅              |
| Created by              | Collection | `iter()` | `yield` / `()` |

---

## 5️⃣ Real-World Analogy

📚 **Iterable** → Book
📖 **Iterator** → Bookmark inside the book
🖨 **Generator** → Printer that prints one page when requested

---

## 6️⃣ Why Generators Are Important (Interview Point)

* Memory efficient for large data
* Faster startup time
* Ideal for:

  * File reading
  * Streaming data
  * Large ML datasets
  * Pipelines in MLOps

Example:

```python
def read_large_file(file_path):
    with open(file_path) as f:
        for line in f:
            yield line
```

---

## 7️⃣ Summary (1-liner)

* **Iterable**: Something you can loop over
* **Iterator**: Object that gives values one by one
* **Generator**: Easy and efficient way to create iterators

---
