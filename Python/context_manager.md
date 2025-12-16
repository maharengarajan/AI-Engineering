# **Context Manager**

A **context manager** in Python is a construct that lets you **set up a resource**, use it safely, and **clean it up automatically**, even if an error occurs.

The most common way you’ve seen it is with the **`with` statement**.

---

## Simple example

```python
with open("data.txt", "r") as f:
    data = f.read()
```

Here:

* File is **opened**
* You read the file
* File is **automatically closed**, even if an exception happens

No need to call `f.close()` manually.

---

## Why context managers are needed

Without a context manager:

```python
f = open("data.txt", "r")
data = f.read()
f.close()
```

If an error occurs before `f.close()`:

* File stays open ❌
* Resource leak happens ❌

Context managers solve this by guaranteeing **cleanup**.

---

## What problems context managers solve

They help manage resources like:

* Files
* Database connections
* Network sockets
* Locks (threading / multiprocessing)
* GPU memory / ML sessions
* Temporary files

Key benefits:

1. **Automatic cleanup**
2. **Exception-safe**
3. **Cleaner, readable code**
4. **Less boilerplate**

---

## How context managers work internally

A context manager defines **two methods**:

| Method        | Purpose                    |
| ------------- | -------------------------- |
| `__enter__()` | Setup (allocate resource)  |
| `__exit__()`  | Cleanup (release resource) |

---

## Custom context manager (class-based)

```python
class FileManager:
    def __enter__(self):
        print("Opening file")
        self.file = open("data.txt", "r")
        return self.file

    def __exit__(self, exc_type, exc_value, traceback):
        print("Closing file")
        self.file.close()
```

Usage:

```python
with FileManager() as f:
    print(f.read())
```

Execution flow:

1. `__enter__()` runs
2. Code inside `with` block runs
3. `__exit__()` runs **even if exception occurs**

---

## Handling exceptions in `__exit__`

```python
def __exit__(self, exc_type, exc_value, traceback):
    if exc_type:
        print("Exception occurred")
    return True   # suppress exception
```

* Return `True` → exception is suppressed
* Return `False` or `None` → exception is raised normally

---

## Context manager using `contextlib` (recommended)

Simpler and cleaner:

```python
from contextlib import contextmanager

@contextmanager
def file_manager():
    print("Opening file")
    f = open("data.txt", "r")
    try:
        yield f
    finally:
        print("Closing file")
        f.close()
```

Usage:

```python
with file_manager() as f:
    print(f.read())
```

---

## Real-world examples you already use

```python
with open("file.txt") as f:
    ...

with lock:
    ...

with torch.no_grad():
    ...

with db_connection:
    ...
```

In **ML/MLOps**, context managers are heavily used:

* `torch.no_grad()` → disables gradient tracking
* `tf.device()` → device placement
* Database sessions
* Temporary experiment tracking (MLflow)

---

## One-line intuition

> **A context manager ensures “do something before” and “always clean up after” a block of code.**

---