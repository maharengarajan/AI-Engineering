### **1. Abstract Classes**

An **abstract class** is a class that **cannot be instantiated directly** and is meant to be **subclassed**. It can have:

* **Abstract methods** (declared but not implemented — subclasses must implement them)
* **Concrete methods** (fully implemented methods)
* **Member variables** (fields)
* **Constructors** (to initialize fields)

**Key points:**

| Feature                            | Abstract Class                                                                         |
| ---------------------------------- | -------------------------------------------------------------------------------------- |
| Can contain method implementations | ✅ Yes                                                                                  |
| Can contain abstract methods       | ✅ Yes                                                                                  |
| Can have member variables/fields   | ✅ Yes                                                                                  |
| Can have constructors              | ✅ Yes                                                                                  |
| Inheritance                        | Single inheritance in languages like Java (a class can extend only one abstract class) |
| Use case                           | When you want to share **code + contract** across related classes                      |

**Example in Python:**

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    def __init__(self, name):
        self.name = name  # concrete property

    @abstractmethod
    def make_sound(self):
        pass  # abstract method

    def sleep(self):
        print(f"{self.name} is sleeping")  # concrete method

class Dog(Animal):
    def make_sound(self):
        print("Woof Woof!")

dog = Dog("Buddy")
dog.make_sound()  # Woof Woof!
dog.sleep()       # Buddy is sleeping
```

---

### **2. Interfaces**

An **interface** is a **contract** that defines **methods without implementation**. A class that implements an interface **must provide implementations** for all the methods defined in the interface.

**Key points:**

| Feature                            | Interface                                                                          |
| ---------------------------------- | ---------------------------------------------------------------------------------- |
| Can contain method implementations | ❌ Usually no (some languages like Java 8+ allow default methods)                   |
| Can contain abstract methods       | ✅ Yes, all methods are abstract by default                                         |
| Can have member variables/fields   | ❌ Typically no (constants allowed in some languages)                               |
| Can have constructors              | ❌ No                                                                               |
| Inheritance                        | A class can implement **multiple interfaces** (solves multiple inheritance issues) |
| Use case                           | When you want to enforce a **contract** without sharing code                       |

**Example in Python (using ABC as interface):**

```python
from abc import ABC, abstractmethod

class Flyable(ABC):
    @abstractmethod
    def fly(self):
        pass

class Bird(Flyable):
    def fly(self):
        print("Bird is flying")

class Airplane(Flyable):
    def fly(self):
        print("Airplane is flying")

bird = Bird()
bird.fly()       # Bird is flying
plane = Airplane()
plane.fly()      # Airplane is flying
```

---

### **3. Key Differences**

| Feature     | Abstract Class                       | Interface                                                 |
| ----------- | ------------------------------------ | --------------------------------------------------------- |
| Purpose     | Share code + enforce some rules      | Enforce rules only (contract)                             |
| Methods     | Can have abstract + concrete methods | Usually abstract only (Java allows default methods)       |
| Fields      | Can have instance variables          | Only constants (in Java)                                  |
| Inheritance | Single (in Java, C#)                 | Multiple allowed                                          |
| Constructor | Allowed                              | Not allowed                                               |
| When to use | When classes are **closely related** | When classes are **unrelated but need to share behavior** |

---

**💡 Tip:**

* Use **abstract class** when you want **code reuse** and some level of abstraction.
* Use **interface** when you want to define **common behavior** across **unrelated classes**.

---