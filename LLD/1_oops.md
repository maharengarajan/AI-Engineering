# Low Level Design Topics

# ✅ **What is OOP (Object-Oriented Programming)?**

**OOP is a programming paradigm** (a way of writing programs) that organizes code into **objects**.
Objects represent **real-world things** with:

* **Data** — also called *attributes*
* **Behaviors** — also called *methods* (functions inside class)

In simple words:

> **OOP helps you structure your code like real-world objects — making it reusable, modular, and easier to maintain.**

---

# 🎯 **Why OOP? (The Core Idea)**

Without OOP, your program becomes a big block of functions and variables → difficult to manage.

With OOP, you group related things together:

```
Car  → attributes: color, model, speed  
      methods: start(), stop(), accelerate()
```

This makes programs:

* Easier to understand
* Easier to scale
* Easier to reuse
* More secure (via encapsulation)

---

# 🧱 **4 Pillars of OOP (Very Important)**

These are the foundations of OOP:

1. **Class** — Blueprint
2. **Object** — Instance of a class
3. **Encapsulation** — Data hiding
4. **Inheritance** — Reusing & extending code
5. **Polymorphism** — Same function name, different behavior
6. **Abstraction** — Show essential details, hide complexity


---

# 🏗️ **What is a Class?**

A **class** is a *blueprint* or *template* for creating objects.

Example:

```python
class Person:
    pass
```

This doesn't do anything yet, but it's a class.

---

# 🧍 **What is an Object?**

An **object** is created (instantiated) from a class.

```python
p = Person()
```

Here, `p` is an object.

---

# 🎉 Let's Create a Real Class with Attributes and Methods

Example:

```python
class Person:
    def __init__(self, name, age):
        self.name = name      # attribute
        self.age = age

    def say_hello(self):       # method
        print(f"Hello, I am {self.name} and I am {self.age} years old.")
```

### Create an object:

```python
p1 = Person("Renga", 30)
p1.say_hello()
```

Output:

```
Hello, I am Renga and I am 30 years old.
```

---

# 🧠 **Understanding the `__init__` method**

`__init__` is a **constructor** — it runs automatically when an object is created.

* `self` refers to the current object
* `self.name` becomes an attribute stored inside the object

---

# 🔍 Visual breakdown

```
Class: Person
----------------------------------
Attributes: name, age
Methods: say_hello()
```

Object:

```
p1 → Person("Renga", 30)
p1.name = "Renga"
p1.age = 30
```

---

# 🧱 **1. Instance Variables**

### ✔️ What are they?

* Variables **unique to each object**.
* Defined **inside `__init__`** using `self`.
* Each object gets its **own copy**.

### Example:

```python
class Person:
    def __init__(self, name, age):
        self.name = name        # instance variable
        self.age = age          # instance variable
```

### Creating objects:

```python
p1 = Person("Renga", 30)
p2 = Person("Raj", 25)
```

Here:

* `p1.name = "Renga"`
* `p2.name = "Raj"`

👉 Both have **different values**
👉 Changing one **does not affect** the other

---

# 🏛️ **2. Class Variables**

### ✔️ What are they?

* Variables **shared by all objects**.
* Defined **outside `__init__`**, inside the class block.
* Only **one copy exists**, shared across all objects.

### Example:

```python
class Person:
    species = "Human"     # class variable

    def __init__(self, name, age):
        self.name = name
        self.age = age
```

Creating objects:

```python
p1 = Person("Renga", 30)
p2 = Person("Raj", 25)
```

### Accessing:

```python
print(p1.species)   # Human
print(p2.species)   # Human
```

If we change the class variable:

```python
Person.species = "Homo Sapiens"
```

Both objects see the new value:

```
p1.species → Homo Sapiens  
p2.species → Homo Sapiens
```

👉 All objects share one variable
👉 Updating it affects every instance

---

# 🔥 classmethod and staticmethod

Not all methods in a class should operate on object data (`self`).
Sometimes we need a method that:

* Works on the **class itself**, not objects → *class method*
* Does **not depend on class or instance** → *static method*

---

# 🧱 1. **Instance Method** (for comparison)

```python
def method(self):
```

* Uses **object data**
* Most common method type

Example:

```python
class Person:
    def say_hello(self):
        print("Hello!")
```

---

# 🏛️ 2. **Class Method**

### ✔️ Key Points:

* Uses `@classmethod` decorator
* First argument is `cls` (refers to **class**, not object)
* Can access/modify **class variables**
* Called using class or object

### Example:

```python
class Person:
    species = "Human"    # class variable

    @classmethod
    def change_species(cls, new_species):
        cls.species = new_species
```

Using it:

```python
Person.change_species("Homo Sapiens")
```

Now:

```
Person.species → Homo Sapiens
For all objects → Homo Sapiens
```

👉 **Use class method when you want to work with class-level data.**

---

# ⭐ Another use: Alternative constructor

Class methods are often used as **multiple constructors**:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    @classmethod
    def from_string(cls, data_str):
        name, age = data_str.split("-")
        return cls(name, int(age))
```

Usage:

```python
p = Person.from_string("Renga-30")
```

---

# 🧊 3. **Static Method**

### ✔️ Key Points:

* Uses `@staticmethod` decorator
* Does **not** use `self`
* Does **not** use `cls`
* Behaves like a **normal function** inside a class
* Used when method is **logically related to class** but doesn’t need class or instance data

### Example:

```python
class MathUtils:
    @staticmethod
    def add(a, b):
        return a + b
```

Using it:

```python
print(MathUtils.add(5, 3))  # 8
```

👉 **Use static method when logic belongs to the class group, but doesn't require class/instance.**

---

# 💬 Summary in One Line

* **Instance method** → works with **object**
* **Class method** → works with **class**
* **Static method** → helper/utility; works with **neither**

---
Great choice! **Inheritance** is one of the most important concepts in OOP.
Let’s understand it clearly and simply, with Python examples.

---

# 🧬 **What is Inheritance?**

**Inheritance allows one class to acquire the properties and methods of another class.**

* The class that is inherited from is called **Parent class** / **Base class**
* The class that inherits is called **Child class** / **Derived class**

👉 It helps in **code reuse**, avoids duplication, and makes programs cleaner.

---

# 🧱 Basic Example of Inheritance

```python
class Animal:                     # Parent class
    def speak(self):
        print("Animal makes a sound")

class Dog(Animal):                # Child class
    pass
```

```python
d = Dog()
d.speak()     # Output: Animal makes a sound
```

✔️ `Dog` inherited the `speak()` method
✔️ Child class can use parent methods automatically

---

# 🔥 Why Inheritance is Useful?

Imagine you have multiple animals:

```python
class Animal:
    def eat(self):
        print("Eating...")
```

Instead of writing `eat()` in every animal class, we put it in `Animal`, and all subclasses get it.

---

# 🧩 **1. Single Inheritance**

One parent → one child.

```
Animal → Dog
```

Example:

```python
class Animal:
    def eat(self):
        print("Eating...")

class Dog(Animal):
    def bark(self):
        print("Barking...")
```

```python
d = Dog()
d.eat()
d.bark()
```

---

# 🧩 **2. Multi-level Inheritance**

Child → Parent
Grandchild → Child

```
Animal → Mammal → Dog
```

Example:

```python
class Animal:
    def eat(self):
        print("Eating")

class Mammal(Animal):
    def walk(self):
        print("Walking")

class Dog(Mammal):
    def bark(self):
        print("Barking")
```

```python
d = Dog()
d.eat()
d.walk()
d.bark()
```

---

# 🧩 **3. Multiple Inheritance**

Child inherits from **multiple parents**.

```
A   B
 \ /
  C
```

Example:

```python
class Father:
    def skills(self):
        print("Farming")

class Mother:
    def skills(self):
        print("Cooking")

class Child(Father, Mother):
    pass
```

```python
c = Child()
c.skills()    # Output: Farming  (because Father is first in order)
```

👉 Because Python follows **MRO (Method Resolution Order)**
👉 Searches left → right in parent classes

---

# 🧩 **4. Hierarchical Inheritance**

One parent → multiple children.

```
      Animal
     /   |   \
  Dog   Cat  Horse
```

Example:

```python
class Animal:
    def eat(self):
        print("Eating")

class Dog(Animal):
    pass

class Cat(Animal):
    pass
```

---

# 🧩 **5. Hybrid Inheritance**

Combination of multiple types.

Python supports it, but it can get confusing.
(We’ll revisit after we cover MRO.)

---

# ⭐ Method Overriding (Very Important)

Child class can **override** parent methods.

```python
class Animal:
    def speak(self):
        print("Animal sound")

class Dog(Animal):
    def speak(self):
        print("Bark!")
```

```python
d = Dog()
d.speak()   # Bark!
```

---

# ⭐ Using `super()` (Very Important)

Used to call parent class methods.

Example:

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)    # call parent constructor
        self.breed = breed
```

```python
d = Dog("Tommy", "Labrador")
print(d.name, d.breed)
```

---

# 🎯 Why `super()` is useful?

* Avoids duplicate code
* Works well with multiple inheritance
* Ensures parent logic is executed

---

# 📌 Summary

| Type         | Meaning                              |
| ------------ | ------------------------------------ |
| Single       | One parent → One child               |
| Multi-level  | Chain of inheritance                 |
| Multiple     | Child inherits from multiple parents |
| Hierarchical | One parent → many children           |
| Hybrid       | Combination                          |

---

# ✔️ Inheritance Benefits

* Reusability
* Cleaner code
* Avoid duplication
* Supports polymorphism
* Easier maintenance

---
Great! Let’s explore **Polymorphism** — one of the most powerful concepts in OOP.
I’ll explain it clearly with Python examples.

---

# 🎭 **What is Polymorphism?**

**Polymorphism** means:

> **Same function name, different behavior**
> (based on which object is calling it)

The word "poly" means **many**
"morph" means **forms**

So polymorphism = **many forms**

---

# 🧩 Types of Polymorphism in Python

Python supports:

1. **Method Overriding** (Runtime Polymorphism)
2. **Method Overloading** (Not built-in, can be simulated)
3. **Operator Overloading**
4. **Duck Typing** (Python-specific)

Let’s understand each clearly.

---

# 1️⃣ **Method Overriding (Most Important)**

Child class overrides the method of parent class.

### Example:

```python
class Animal:
    def sound(self):
        print("Animal makes a sound")

class Dog(Animal):
    def sound(self):
        print("Bark!")

class Cat(Animal):
    def sound(self):
        print("Meow!")
```

Usage:

```python
for animal in [Dog(), Cat()]:
    animal.sound()
```

Output:

```
Bark!
Meow!
```

👉 Same method name → different behavior
👉 Depends on object type

This is **runtime polymorphism**.

---

# 2️⃣ **Duck Typing (Python’s special feature)**

In Python, type doesn't matter — only behavior matters.

> “If it walks like a duck and quacks like a duck, it's a duck.”

Example:

```python
class Dog:
    def speak(self):
        print("Bark!")

class Human:
    def speak(self):
        print("Hello!")

def make_it_speak(obj):
    obj.speak()
```

Call:

```python
make_it_speak(Dog())
make_it_speak(Human())
```

Output:

```
Bark!
Hello!
```

👉 No inheritance needed
👉 Works as long as the object has `speak()`
👉 This is strong polymorphism + flexibility

---

# 3️⃣ **Operator Overloading**

Python allows operators like `+`, `>`, `==` to behave differently for custom objects.

Example:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Point(self.x + other.x, self.y + other.y)
```

Usage:

```python
p1 = Point(1, 2)
p2 = Point(3, 4)
p3 = p1 + p2   # uses __add__
```

👉 The `+` operator behaves differently for strings, numbers, and your custom class.

Other operators you can overload:

| Operator | Method    |
| -------- | --------- |
| +        | `__add__` |
| -        | `__sub__` |
| *        | `__mul__` |
| <        | `__lt__`  |
| >        | `__gt__`  |
| ==       | `__eq__`  |

---

# 4️⃣ **Method Overloading (Not native in Python)**

Python does **not** support true method overloading like Java/C++.

But since Python is dynamic, you can simulate it.

Example:

```python
class Math:
    def add(self, a, b=0, c=0):
        return a + b + c
```

Usage:

```python
print(Math().add(1, 2))      # 3
print(Math().add(1, 2, 3))   # 6
```

👉 One method behaves like multiple methods
👉 By using default arguments

---

# 📌 Why Polymorphism is Important?

* Makes code flexible
* Reduces duplication
* Allows writing clean, extensible systems
* Enables plug-and-play type behavior
* Supports OCP principle (Open-Closed Principle)

---

# 🎭 **What is Abstraction?**

**Abstraction** means:

> Hiding unnecessary details and showing only the essential features.

You interact with things **without knowing how they work internally**.

Examples in real life:

* You drive a car → you don’t need to know engine internals
* You use a TV remote → you don’t need to know the circuits
* You use a mobile → you don’t know how CPU, RAM, OS works

The idea:

✔️ Focus on *what it does*
❌ Ignore *how it does*

---

# 🧱 In Python, how do we implement Abstraction?

Python provides abstraction using:

### ✔️ `abc` module

(ABC → Abstract Base Class)

### Important tools:

* `ABC` class
* `@abstractmethod` decorator

---

# 🛠️ **1️⃣ Creating an Abstract Class**

An abstract class:

* **cannot be instantiated directly**
* must have one or more **abstract methods**
* child classes must implement those abstract methods

Example:

```python
from abc import ABC, abstractmethod

class Animal(ABC):

    @abstractmethod
    def sound(self):
        pass    # only a declaration
```

Try to create object:

```python
a = Animal()   # ❌ Error: Can't instantiate abstract class
```

---

# 🐶 **2️⃣ Inheriting & implementing abstract methods**

```python
class Dog(Animal):
    def sound(self):
        return "Bark"

class Cat(Animal):
    def sound(self):
        return "Meow"
```

Usage:

```python
animals = [Dog(), Cat()]

for a in animals:
    print(a.sound())
```

Output:

```
Bark
Meow
```

👉 Parent class forces children to implement `sound()`
👉 This is abstraction.

---

# 🧩 Why do we need abstraction?

### ✔️ To define a *contract*

Parent class defines **what must be implemented**

### ✔️ To enforce a structure

Child classes must implement certain methods

### ✔️ Useful in large applications

When many developers work on same project

### ✔️ Helps design clean, scalable systems

(e.g., payment systems, file readers, database connectors)

---

# 📦 **3️⃣ Real-World Example: Payment System**

Parent abstract class:

```python
from abc import ABC, abstractmethod

class Payment(ABC):

    @abstractmethod
    def pay(self, amount):
        pass
```

Child classes:

```python
class UPI(Payment):
    def pay(self, amount):
        print(f"Paid {amount} via UPI")

class Card(Payment):
    def pay(self, amount):
        print(f"Paid {amount} using Credit/Debit Card")

class NetBanking(Payment):
    def pay(self, amount):
        print(f"Paid {amount} via NetBanking")
```

Usage:

```python
def make_payment(payment_method, amount):
    payment_method.pay(amount)
```

```python
make_payment(UPI(), 500)
make_payment(Card(), 1000)
```

Output:

```
Paid 500 via UPI
Paid 1000 using Credit/Debit Card
```

👉 The function never needs to know how payment happens.
👉 Abstraction hides details.

---

# 🧠 **4️⃣ Abstract Class with Concrete Methods**

Abstract classes can have **normal methods** too.

```python
class Vehicle(ABC):

    @abstractmethod
    def start(self):
        pass

    def info(self):   # concrete method
        print("Vehicle has engine and wheels")
```

Child class:

```python
class Car(Vehicle):
    def start(self):
        print("Car started with key")
```

---

# 🧪 **5️⃣ We can also have abstract properties (optional)**

```python
from abc import ABC, abstractproperty

class Shape(ABC):

    @property
    @abstractmethod
    def area(self):
        pass
```

Child:

```python
class Square(Shape):
    def __init__(self, side):
        self.side = side

    @property
    def area(self):
        return self.side * self.side
```

---

# 🧷 Summary

* Abstraction = Hide complexity, show only essentials
* Done using abstract classes & abstract methods
* Child classes must override abstract methods
* Helps enforce rules/structure
* Makes code scalable and maintainable

---
# 🧱 **What is Encapsulation?**

**Encapsulation** means:

> **Wrapping data (variables) and methods (functions) into a single unit (class)
> and controlling access to them.**

It is mainly used to:

* Protect data
* Restrict direct access
* Prevent accidental modification
* Expose only what is necessary

In simple words:

👉 Encapsulation = **Data Protection**

---

# 🛡️ Why do we need Encapsulation?

Without encapsulation:

* Anyone can change any value
* Data can get corrupted
* Harder to maintain code
* No control over how data is modified

Encapsulation helps by giving:

✔ Controlled access
✔ Getters and setters
✔ Hidden (private) variables
✔ Safety

---

# 🔐 Levels of Access (Python)

Python does not have strict private/protected keywords like Java or C++,
but it follows **naming conventions** to enforce access control.

### 1️⃣ **Public**

Can be accessed anywhere
(No underscore)

```python
self.name
```

### 2️⃣ **Protected**

Presents a “soft” restriction
(Single underscore `_`)

```python
self._salary
```

Meaning: “This is for internal use. Don’t touch from outside.”

### 3️⃣ **Private**

Strong restriction
(Double underscore `__`)

```python
self.__bank_balance
```

Python does *name mangling* for private variables:

`__bank_balance` → internally becomes `_ClassName__bank_balance`

---

# 🧪 Example of All Three

```python
class Employee:
    def __init__(self):
        self.name = "Renga"            # public
        self._salary = 50000           # protected
        self.__bank_balance = 200000   # private
```

Access:

```python
e = Employee()
print(e.name)         # OK
print(e._salary)      # Possible but not recommended
print(e.__bank_balance)  # ❌ Error: AttributeError
```

---

# 🔧 Accessing Private Variables (Getter & Setter)

To access private data safely, we use **getters** and **setters**.

### Example:

```python
class Employee:

    def __init__(self, salary):
        self.__salary = salary    # private

    # getter
    def get_salary(self):
        return self.__salary

    # setter
    def set_salary(self, amount):
        if amount > 0:
            self.__salary = amount
        else:
            print("Invalid salary!")
```

Usage:

```python
e = Employee(50000)
print(e.get_salary())       # 50000
e.set_salary(60000)
print(e.get_salary())       # 60000
e.set_salary(-100)          # Invalid salary!
```

👉 Private variables cannot be accessed directly
👉 But can be accessed through controlled methods

---

# ⭐ Why use getters and setters?

* Validation (ex: salary cannot be negative)
* Security (ex: password should not be accessed directly)
* Controlled modification
* Hiding sensitive data

---

# 🧩 Real-World Example: Bank Account

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance   # private variable

    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount
        else:
            print("Invalid deposit amount")

    def withdraw(self, amount):
        if 0 < amount <= self.__balance:
            self.__balance -= amount
        else:
            print("Insufficient funds")

    def get_balance(self):
        return self.__balance
```

Usage:

```python
acc = BankAccount(1000)
acc.deposit(500)
acc.withdraw(300)
print(acc.get_balance())  # 1200
```

👉 Direct modification is not allowed:
`acc.__balance = 999999` won't change the actual private variable.

---

# ✔️ Summary

* Encapsulation bundles data + methods together
* Protects data using public/protected/private conventions
* Private variables use name mangling
* Use getters and setters to access private data safely
* Improves security and maintainability

---
# ✅ **Class Methods vs Static Methods in Python**

Before we dive in, remember:

* **Instance method** → Works on *objects*
* **Class method** → Works on *class*
* **Static method** → Works on *neither object nor class directly*, just grouped inside the class for logical reasons

Let’s break it down.

---

# 🟦 **1. Class Methods**

A **class method** is a method that works with the *class itself*, not the object (instance).

### ✔ Key points

* Uses `@classmethod` decorator
* First argument is `cls` (points to the *class*)
* Can access **class variables**
* Can modify **class state**
* Often used for **factory methods** (alternate constructors)

### Example

```python
class Employee:
    company = "Google"   # class variable

    def __init__(self, name):
        self.name = name

    @classmethod
    def change_company(cls, new_company):
        cls.company = new_company  # modifies class variable
```

### Usage

```python
print(Employee.company)    # Google
Employee.change_company("Amazon")
print(Employee.company)    # Amazon
```

✔ Even without creating an object, you can call it.
✔ Every object will see the updated value.

---

# 🟩 **2. Static Methods**

A **static method** is just a normal function **inside a class**, but it does not access:

* `self` (instance)
* `cls` (class)

It’s simply used for **grouping utility/helper functions** inside a class for better organization.

### ✔ Key points

* Uses `@staticmethod` decorator
* Does NOT know about class or instance
* Used when you want a function that is related to the class logically but doesn’t need class data

### Example

```python
class MathUtils:

    @staticmethod
    def add(a, b):
        return a + b
```

### Usage

```python
print(MathUtils.add(3, 5))   # 8
```

✔ No object needed
✔ No class state accessed

---

# 🔵 **Class Method vs Static Method: When to Use?**

| Feature / Purpose         | Class Method                            | Static Method            |
| ------------------------- | --------------------------------------- | ------------------------ |
| Access class variables    | ✔ Yes                                   | ❌ No                     |
| Access instance variables | ❌ No                                    | ❌ No                     |
| First argument            | `cls`                                   | nothing                  |
| Used for                  | class-level operations, factory methods | utility/helper functions |
| Can modify class state    | ✔ Yes                                   | ❌ No                     |

---

# 🎯 Summary

### **Class Method**

* Works with the class (`cls`)
* Modifies class variables
* Used for factory methods

### **Static Method**

* Utility/helper function
* Doesn’t use `self` or `cls`
* Just placed inside the class for logical grouping

---
# 🟦 **What is a Decorator in Python?**

A **decorator** is a function that:

### ✔ Takes another function as input

### ✔ Adds extra functionality

### ✔ Returns a new function

Without changing the original function’s code.

Think of it as **wrapping** a function with extra behavior.

---

# 🎯 Simple Definition

👉 **A decorator modifies or enhances a function without changing the function's actual code.**

---

# 📦 Real–world analogy

Imagine you have a plain gift box (your function).
You wrap it with decorative paper and ribbon (decorator).
The gift inside is the same, but the appearance/behavior changes.

---

# 🟦 **Basic Decorator Example**

Let's start with the simplest form.

```python
def my_decorator(func):
    def wrapper():
        print("Before the function runs")
        func()
        print("After the function runs")
    return wrapper
```

Using the decorator:

```python
@my_decorator
def say_hello():
    print("Hello!")

say_hello()
```

### Output:

```
Before the function runs
Hello!
After the function runs
```

👉 The `@my_decorator` wraps the function and adds new behavior.

---

# 🟩 **Why do we need decorators?**

Decorators help in:

✔ Logging
✔ Authentication
✔ Timing/performance measurement
✔ Caching
✔ Access control
✔ Validation
✔ Reusing cross-cutting features across functions

---

# 🟦 **Another Example: Logging Decorator**

```python
def log(func):
    def wrapper(*args, **kwargs):
        print(f"Calling function: {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log
def add(a, b):
    return a + b

print(add(5, 3))
```

Output:

```
Calling function: add
8
```

---

# 🟥 How `@decorator` actually works internally

This:

```python
@log
def add(a, b):
    return a + b
```

Is same as:

```python
def add(a, b):
    return a + b

add = log(add)  # wrapping
```

---

# 🟧 Decorators With Arguments

If the decorator itself needs arguments:

```python
def repeat(n):
    def decorator(func):
        def wrapper():
            for _ in range(n):
                func()
        return wrapper
    return decorator
```

Usage:

```python
@repeat(3)
def greet():
    print("Hello")
```

Output:

```
Hello
Hello
Hello
```

---

# 🟦 Decorators with `@property`, `@staticmethod`, `@classmethod`

These are **built-in decorators**.

Example:

```python
class Test:
    @staticmethod
    def util():
        print("I don't need self or cls")
```

They behave exactly like custom decorators — they wrap and enhance methods.

---

# 🟩 Summary

| Concept   | Explanation                                                |
| --------- | ---------------------------------------------------------- |
| Decorator | A function that enhances another function                  |
| Syntax    | `@decorator_name`                                          |
| Input     | A function                                                 |
| Output    | A new modified function                                    |
| Uses      | Logging, validation, access control, timing, caching, etc. |

---