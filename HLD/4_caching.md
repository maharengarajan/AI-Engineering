## *Caching*

Caching is a **performance optimization technique** where frequently accessed data is stored in a **faster storage layer** so future requests can be served **quickly without recomputing or refetching** from the original source.

---

## 1. What is Caching?

**Caching = storing a copy of data in a temporary, fast-access storage (cache)**

Instead of:

```
Client → Server → Database
```

We try:

```
Client → Cache → (if miss) → Server → Database
```

### Simple example

* User requests profile data
* Server fetches from DB → takes 100 ms
* Store result in cache
* Next request → served from cache → 5 ms

---

## 2. Why Caching is Important

### Key benefits

✅ **Reduced latency**
✅ **Reduced database load**
✅ **Higher throughput (QPS)**
✅ **Lower cost** (fewer DB reads, API calls)
✅ **Better scalability**

---

## 3. Cache Hit vs Cache Miss

### Cache Hit

Data exists in cache → returned immediately

### Cache Miss

Data not in cache → fetch from source → store in cache

```
if key in cache:
    return cache[key]   # hit
else:
    data = fetch_from_db()
    cache[key] = data   # miss
    return data
```

---

## 4. Where Can Caching Happen? (Levels of Caching)

### 1️⃣ Client-side cache

* Browser cache
* Mobile app memory cache

**Examples**

* Images
* CSS, JS
* API responses

---

### 2️⃣ CDN Cache

* Caches content closer to users
* Used for static content

**Examples**

* CloudFront
* Cloudflare
* Akamai

```
User → CDN → Origin Server
```

---

### 3️⃣ Application-level cache

* In-memory caches used by backend services

**Examples**

* Redis
* Memcached
* Local in-memory cache

---

### 4️⃣ Database cache

* Query cache
* Buffer pool

**Examples**

* MySQL buffer pool
* PostgreSQL shared buffers

---

## 5. Types of Cache Based on Storage

### 🔹 In-memory Cache

* Very fast
* Data lost on restart

**Examples**

* Redis
* Memcached
* LRU cache in application

---

### 🔹 Disk-based Cache

* Slower than memory
* Survives restarts

**Examples**

* CDN disk cache
* OS page cache

---

## 6. Cache Eviction Policies

When cache is full, something must be removed.

### Common strategies

| Policy   | Description           |
| -------- | --------------------- |
| **LRU**  | Least Recently Used   |
| **LFU**  | Least Frequently Used |
| **FIFO** | First In First Out    |
| **TTL**  | Time-based expiration |

**Redis example**

```text
maxmemory-policy allkeys-lru
```

---

## 7. Cache Expiration (TTL)

TTL = **Time To Live**

```
cache.set("user_123", data, ttl=300)  # 5 minutes
```

Why TTL?

* Prevent stale data
* Automatic cleanup

---

## 8. Cache Consistency Problem

Cached data can become **stale** if the source changes.

### Example

* User updates profile
* Cache still has old data

---

## 9. Cache Invalidation Strategies (Very Important)

> “There are only two hard problems in computer science: cache invalidation and naming things.”

### 1️⃣ Write-through

* Update cache and DB together

```
write → cache → DB
```

✅ Strong consistency
❌ Slower writes

---

### 2️⃣ Write-around

* Write directly to DB
* Cache updated later on read

✅ Reduces cache pollution
❌ Initial read miss

---

### 3️⃣ Write-back (Write-behind)

* Write to cache first
* Async write to DB

✅ Fast writes
❌ Risk of data loss

---

### 4️⃣ Explicit invalidation

* Remove cache when data changes

```
update user → delete cache(user_id)
```

---

## 10. Common Caching Patterns (HLD Favorite)

### 🔹 Cache-Aside (Lazy Loading) ⭐ Most Common

```
Read:
- Check cache
- On miss → read DB → update cache

Write:
- Update DB
- Invalidate cache
```

Used in:

* Microservices
* REST APIs

---

### 🔹 Read-through Cache

Cache fetches data automatically from DB

---

### 🔹 Write-through Cache

Writes go to cache → cache writes to DB

---

## 11. Caching in Distributed Systems

### Challenges

❌ Cache consistency
❌ Cache stampede
❌ Hot keys
❌ Network latency

### Cache Stampede

Many requests hit DB when cache expires

**Solution**

* Locking
* Request coalescing
* Staggered TTL

---

## 12. Caching in MLOps / AI Systems (Relevant to You)

### Examples

* Cache embeddings (Redis + FAISS)
* Cache inference results
* Cache feature store values
* Cache model metadata

```
API → Redis (embeddings) → Model
```

This **reduces inference latency significantly**.

---

## 13. When NOT to Use Caching

❌ Highly volatile data
❌ Strong consistency required
❌ Low read frequency
❌ Sensitive data without encryption

---

## 14. Summary (Interview-Friendly)

> **Caching improves performance and scalability by storing frequently accessed data in fast storage layers. It introduces challenges like cache consistency and invalidation, which are handled using strategies such as TTL, eviction policies, and patterns like cache-aside.**

---

## **Python caching examples**

Below are **practical Python caching examples**, starting from **basic → real-world system design patterns**. These are commonly expected in **interviews and production systems**.

---

## 1️⃣ Simple In-Memory Cache (Dictionary)

```python
cache = {}

def get_square(n):
    if n in cache:               # cache hit
        print("Cache hit")
        return cache[n]
    print("Cache miss")
    result = n * n
    cache[n] = result            # store in cache
    return result

print(get_square(4))
print(get_square(4))
```

---

## 2️⃣ LRU Cache (Most Common Interview Example)

Python provides this out of the box.

```python
from functools import lru_cache

@lru_cache(maxsize=3)
def get_data(x):
    print("Fetching from source...")
    return x * 10

print(get_data(1))
print(get_data(2))
print(get_data(3))
print(get_data(1))  # cache hit
print(get_data(4))  # evicts least recently used
```

✅ Automatically handles eviction
❌ Not distributed (single process only)

---

## 3️⃣ Cache with TTL (Time-Based Expiration)

```python
import time

cache = {}

def get_data(key):
    if key in cache:
        value, expiry = cache[key]
        if time.time() < expiry:
            print("Cache hit")
            return value

    print("Cache miss")
    value = key.upper()
    cache[key] = (value, time.time() + 5)  # TTL = 5 sec
    return value

print(get_data("hello"))
time.sleep(3)
print(get_data("hello"))
time.sleep(3)
print(get_data("hello"))  # expired
```

---

## 4️⃣ Cache-Aside Pattern (Production Standard)

```python
def get_user(user_id, cache, database):
    if user_id in cache:
        print("Cache hit")
        return cache[user_id]

    print("Cache miss → DB")
    user = database.get(user_id)
    cache[user_id] = user
    return user

# Simulated DB and Cache
db = {1: "Renga", 2: "Raj"}
redis_cache = {}

print(get_user(1, redis_cache, db))
print(get_user(1, redis_cache, db))
```

👉 **Most used in microservices**

---

## 5️⃣ Redis Cache Example (Real-World)

```python
import redis
import json

redis_client = redis.Redis(host="localhost", port=6379, db=0)

def get_user(user_id):
    cached = redis_client.get(user_id)
    if cached:
        print("Cache hit")
        return json.loads(cached)

    print("Cache miss → DB")
    user = {"id": user_id, "name": "Renga"}
    redis_client.setex(user_id, 60, json.dumps(user))  # TTL = 60s
    return user

print(get_user("user_1"))
print(get_user("user_1"))
```

✅ Distributed
✅ Survives multiple services
⭐ **Industry standard**

---

## 6️⃣ Cache Invalidation on Update

```python
def update_user(user_id, new_name):
    # Update DB
    db[user_id] = new_name

    # Invalidate cache
    redis_cache.pop(user_id, None)
```

> Always invalidate cache after write

---

## 7️⃣ Function Result Caching with Decorator

```python
def cache_decorator(func):
    cache = {}

    def wrapper(*args):
        if args in cache:
            return cache[args]
        cache[args] = func(*args)
        return cache[args]

    return wrapper

@cache_decorator
def slow_function(x):
    time.sleep(2)
    return x * 2

print(slow_function(5))
print(slow_function(5))
```

---

## 8️⃣ Async Cache Example (FastAPI / Async Apps)

```python
import asyncio

cache = {}

async def get_data(key):
    if key in cache:
        return cache[key]

    await asyncio.sleep(1)  # simulate IO
    cache[key] = key * 2
    return cache[key]

async def main():
    print(await get_data(10))
    print(await get_data(10))

asyncio.run(main())
```

---

## 9️⃣ HTTP API Caching (Client-Side)

```python
import requests

headers = {
    "Cache-Control": "max-age=60"
}

response = requests.get("https://api.example.com/data", headers=headers)
print(response.json())
```

---

## 10️⃣ ML / MLOps Example – Cache Model Predictions

```python
prediction_cache = {}

def predict(input_text):
    if input_text in prediction_cache:
        return prediction_cache[input_text]

    # expensive ML inference
    result = len(input_text) * 0.42
    prediction_cache[input_text] = result
    return result
```

Used in:

* Embedding generation
* Inference APIs
* Feature stores

---

## 🔑 Interview Summary Line

> “In Python, caching can be implemented using in-memory dictionaries, `lru_cache`, TTL-based caches, or distributed systems like Redis using cache-aside patterns with proper invalidation strategies.”

---