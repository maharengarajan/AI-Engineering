# SOLID Principles in Python – Complete Documentation

## Introduction

SOLID is a set of five object-oriented design principles that help you write clean, maintainable, scalable, and testable code.
The five principles are:

1. **S** – Single Responsibility Principle
2. **O** – Open/Closed Principle
3. **L** – Liskov Substitution Principle
4. **I** – Interface Segregation Principle
5. **D** – Dependency Inversion Principle

These principles guide you toward better code structure and architecture.

---

# 1. Single Responsibility Principle (SRP)

**Definition:**
A class should have only one reason to change.
This means a class should do only one job.

### Bad Example (violates SRP)

```python
class UserService:
    def create_user(self, data):
        pass

    def send_email(self, email, message):
        pass

    def write_log(self, text):
        pass
```

The class handles user creation, email sending, and logging.
Too many responsibilities.

### Good Example (follows SRP)

```python
class UserService:
    def create_user(self, data):
        pass

class EmailService:
    def send_email(self, email, message):
        pass

class Logger:
    def write_log(self, text):
        pass
```

Each class has a single, well-defined purpose.

---

# 2. Open/Closed Principle (OCP)

**Definition:**
Software entities should be **open for extension** but **closed for modification**.
You should add new behavior without altering existing code.

### Bad Example (violates OCP)

```python
class Discount:
    def calculate(self, customer_type, amount):
        if customer_type == "regular":
            return amount * 0.90
        if customer_type == "vip":
            return amount * 0.80
```

Adding a new customer type means modifying the method, which violates OCP.

### Good Example (follows OCP)

```python
from abc import ABC, abstractmethod

class Discount(ABC):
    @abstractmethod
    def calculate(self, amount):
        pass

class RegularDiscount(Discount):
    def calculate(self, amount):
        return amount * 0.90

class VIPDiscount(Discount):
    def calculate(self, amount):
        return amount * 0.80
```

To add a new discount type, create a new class — no need to modify existing code.

---

# 3. Liskov Substitution Principle (LSP)

**Definition:**
A subclass should be replaceable for its parent class **without breaking the program**.
The child must follow the behavior expected from the parent.

### Bad Example (violates LSP)

```python
class Bird:
    def fly(self):
        return "I can fly"

class Ostrich(Bird):
    def fly(self):
        raise Exception("I can't fly")
```

The subclass breaks the expected behavior of the parent.

### Good Example (follows LSP)

```python
class Bird:
    pass

class FlyingBird(Bird):
    def fly(self):
        return "I can fly"

class Ostrich(Bird):
    pass
```

We reorganize the hierarchy so only birds that can fly are in the `FlyingBird` class.

---

# 4. Interface Segregation Principle (ISP)

**Definition:**
Clients should not be forced to depend on methods they do not use.
In Python, this is implemented using abstract base classes with minimal responsibilities.

### Bad Example (violates ISP)

```python
from abc import ABC, abstractmethod

class Worker(ABC):
    @abstractmethod
    def work(self):
        pass

    @abstractmethod
    def eat(self):
        pass
```

A robot cannot implement `eat()` properly, but is forced to.

```python
class Robot(Worker):
    def work(self):
        pass

    def eat(self):
        raise NotImplementedError()
```

### Good Example (follows ISP)

```python
from abc import ABC, abstractmethod

class Workable(ABC):
    @abstractmethod
    def work(self):
        pass

class Eatable(ABC):
    @abstractmethod
    def eat(self):
        pass
```

Usage:

```python
class Human(Workable, Eatable):
    def work(self):
        pass

    def eat(self):
        pass

class Robot(Workable):
    def work(self):
        pass
```

No class is forced to implement unnecessary methods.

---

# 5. Dependency Inversion Principle (DIP)

**Definition:**
High-level modules should not depend on low-level modules.
Both should depend on abstractions.
Abstractions should not depend on details — details should depend on abstractions.

### Bad Example (violates DIP)

```python
class MySQLDatabase:
    def save(self, data):
        pass

class UserService:
    def __init__(self):
        self.db = MySQLDatabase()  # tightly coupled

    def create_user(self, data):
        self.db.save(data)
```

`UserService` cannot use another database without modifying code.

### Good Example (follows DIP)

```python
from abc import ABC, abstractmethod

class Database(ABC):
    @abstractmethod
    def save(self, data):
        pass

class MySQLDatabase(Database):
    def save(self, data):
        pass

class PostgreSQLDatabase(Database):
    def save(self, data):
        pass
```

High-level module:

```python
class UserService:
    def __init__(self, db: Database):
        self.db = db

    def create_user(self, data):
        self.db.save(data)
```

Now any database can be injected — MySQL, PostgreSQL, MongoDB, mock DB for testing, etc.

---

# Conclusion

SOLID principles help you:

* Reduce bugs
* Increase code readability
* Facilitate easier unit testing
* Make your code scalable for future changes
* Improve design quality and architecture

Mastering SOLID ensures your Python codebase remains robust and easy to work with as your project grows.

---