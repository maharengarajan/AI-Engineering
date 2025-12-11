# Object-Oriented Programming (OOP) in Python

*A concise guide with practical examples from a RAG system*

---

## Table of Contents
1. [What is OOP?](#what-is-oop)
2. [The Four Pillars of OOP](#four-pillars)
3. [Real-World Benefits](#benefits)

---

## What is OOP? {#what-is-oop}

Object-Oriented Programming is a programming paradigm that organizes code into reusable objects containing data and methods.

**Why OOP?**
- ✅ Code reusability
- ✅ Better organization
- ✅ Easier maintenance
- ✅ Flexibility to extend

---

## The Four Pillars of OOP {#four-pillars}

### 1. **Abstraction** 🎭

**Definition**: Hiding complex implementation details, showing only what's necessary.

**How**: Use Abstract Base Classes (ABC) with `@abstractmethod`

**Example**:

```python
from abc import ABC, abstractmethod

# Abstract interface - defines WHAT to do, not HOW
class EmbeddingModelInterface(ABC):
    """Contract for embedding models"""
    
    @abstractmethod
    def get_dense_embedding(self, text: str) -> List[float]:
        """Generate dense embedding - implementation details hidden"""
        pass
    
    @abstractmethod
    def get_sparse_embedding(self, text: str) -> Dict[str, List]:
        """Generate sparse embedding - implementation details hidden"""
        pass
```

**Benefits**:
- Users don't need to know internal complexity
- Clear contract of what methods are available
- Can't instantiate abstract classes directly

---

### 2. **Inheritance** 👨‍👦

**Definition**: Child classes inherit properties and methods from parent classes.

**How**: Use `class Child(Parent):` syntax

**Example**:

```python
# Child class inherits from parent (abstract) class
class RetrievalEmbeddingAdapter(EmbeddingModelInterface):
    """Concrete implementation of the abstract interface"""
    
    def __init__(self, embedding_manager):
        self.embedding_manager = embedding_manager
    
    # Implement inherited abstract method
    def get_dense_embedding(self, text: str) -> List[float]:
        dense_embeddings = self.embedding_manager.embed_dense([text])
        return dense_embeddings[0].values
    
    # Implement inherited abstract method
    def get_sparse_embedding(self, text: str) -> Dict[str, List]:
        sparse_embeddings = self.embedding_manager.embed_sparse([text])
        return {
            "indices": sparse_embeddings[0].indices,
            "values": sparse_embeddings[0].values
        }
```

**Another Example**:

```python
# Parent
class VectorSearchInterface(ABC):
    @abstractmethod
    def search_dense(self, query_vector, collection_name, limit):
        pass

# Child - inherits and implements
class QdrantVectorSearch(VectorSearchInterface):
    def __init__(self, client):
        self.client = client
    
    def search_dense(self, query_vector, collection_name, limit):
        results = self.client.query_points(
            collection_name=collection_name,
            query=query_vector,
            limit=limit
        )
        return results.points
```

**Benefits**:
- Code reuse - don't repeat yourself
- Clear parent-child relationships
- Easy to extend functionality

---

### 3. **Polymorphism** 🎪

**Definition**: "Many forms" - same interface, different implementations.

**How**: Multiple classes implement the same interface differently

**Example**:

```python
# Same interface, different implementations

# Implementation 1: Qdrant
class QdrantVectorSearch(VectorSearchInterface):
    def search_dense(self, query_vector, collection_name, limit):
        # Qdrant-specific implementation
        return self.client.query_points(...)

# Implementation 2: Pinecone
class PineconeVectorSearch(VectorSearchInterface):
    def search_dense(self, query_vector, collection_name, limit):
        # Pinecone-specific implementation
        return self.index.query(...)

# Implementation 3: Weaviate
class WeaviateVectorSearch(VectorSearchInterface):
    def search_dense(self, query_vector, collection_name, limit):
        # Weaviate-specific implementation
        return self.client.search(...)
```

**Polymorphism in Action**:

```python
class HybridSearchRetriever:
    def __init__(
        self,
        vector_search: VectorSearchInterface  # ← Polymorphic parameter
    ):
        self.vector_search = vector_search
    
    def search(self, query):
        # This works with ANY VectorSearchInterface implementation!
        results = self.vector_search.search_dense(query, "collection", 10)
        return results


# Can use ANY implementation - they're interchangeable!
retriever1 = HybridSearchRetriever(QdrantVectorSearch(client))
retriever2 = HybridSearchRetriever(PineconeVectorSearch(index))
retriever3 = HybridSearchRetriever(WeaviateVectorSearch(conn))

# All work the same way!
retriever1.search("query")
retriever2.search("query")
retriever3.search("query")
```

**Benefits**:
- Write code once, work with many implementations
- Easy to swap implementations
- Flexible and extensible

---

### 4. **Encapsulation** 🔒

**Definition**: Bundling data and methods together, hiding internal details.

**How**: Use classes, private attributes (prefix with `_`), and validation

**Example 1 - Data Encapsulation**:

```python
from dataclasses import dataclass

@dataclass
class HybridSearchConfig:
    """Encapsulated configuration with automatic validation"""
    top_k: int = 5
    dense_weight: float = 0.7
    sparse_weight: float = 0.3
    retrieval_multiplier: int = 2

    def __post_init__(self):
        """Validation logic encapsulated inside the class"""
        if self.top_k <= 0:
            raise ValueError(f"top_k must be positive, got {self.top_k}")
        
        if not 0.0 <= self.dense_weight <= 1.0:
            raise ValueError(f"dense_weight must be [0,1], got {self.dense_weight}")


# Usage - validation happens automatically
config = HybridSearchConfig(top_k=10)  # ✅ Valid
config = HybridSearchConfig(top_k=-5)  # ❌ Raises error automatically
```

**Example 2 - Method Encapsulation**:

```python
class QdrantDB:
    """Encapsulates database operations"""
    
    def __init__(self, client):
        self._client = client  # Private (prefix with _)
        self._logger = logging.getLogger(__name__)  # Private
        self._retry_count = 3  # Private configuration
    
    # Public method - users call this
    def upload_points(self, documents, embeddings, collection_name):
        """Public interface - hides complexity"""
        self._validate_inputs(documents, embeddings)  # Private
        points = self._prepare_points(documents, embeddings)  # Private
        return self._upload_with_retry(points, collection_name)  # Private
    
    # Private methods - users don't call these directly
    def _validate_inputs(self, documents, embeddings):
        """Internal validation logic"""
        if len(documents) != len(embeddings):
            raise ValueError("Mismatch in counts")
    
    def _prepare_points(self, documents, embeddings):
        """Internal preparation logic"""
        return [self._create_point(doc, emb) for doc, emb in zip(documents, embeddings)]
    
    def _create_point(self, doc, embedding):
        """Internal point creation"""
        # Complex logic hidden here
        pass
    
    def _upload_with_retry(self, points, collection_name):
        """Internal retry logic"""
        for attempt in range(self._retry_count):
            try:
                return self._client.upsert(collection_name, points)
            except Exception:
                if attempt == self._retry_count - 1:
                    raise
                self._logger.warning(f"Retry {attempt + 1}")


# Usage - simple public interface
db = QdrantDB(client)
db.upload_points(docs, embeddings, "collection")  # Easy to use!

# Users don't see:
# - Retry logic
# - Validation complexity
# - Point preparation details
```

**Benefits**:
- Hide complexity from users
- Protect data with validation
- Easy to change internal implementation
- Clear public interface

---

## Real-World Benefits {#benefits}

### **Why These Principles Matter**

**1. Easy Testing**:
```python
from unittest.mock import Mock

# Mock the interface for testing
mock_search = Mock(spec=VectorSearchInterface)
mock_search.search.return_value = [{"id": "1", "score": 0.9}]

# Test without real database!
retriever = SearchRetriever(mock_search)
results = retriever.retrieve("test", config)
```

**2. Easy Swapping**:
```python
# Switch from Qdrant to Pinecone - just one line change!
# retriever = SearchRetriever(QdrantSearch(client))  # Old
retriever = SearchRetriever(PineconeSearch(index))   # New
```

**3. Clear Structure**:
```python
# Anyone reading the code understands:
# - What interfaces exist (Abstraction)
# - What implementations are available (Inheritance)
# - How to add new implementations (Polymorphism)
# - What's public vs private (Encapsulation)
```

---

## Summary Table

| Principle | What | How | Example |
|-----------|------|-----|---------|
| **Abstraction** | Hide complexity | ABC + @abstractmethod | `EmbeddingModelInterface` |
| **Inheritance** | Reuse code | Child extends Parent | `QdrantSearch(VectorSearchInterface)` |
| **Polymorphism** | Many forms | Same interface, different implementations | `SearchRetriever` accepts any `VectorSearchInterface` |
| **Encapsulation** | Bundle & hide | Private methods (_prefix) + validation | `SearchConfig`, `QdrantDB` |

---