### **What are Design Patterns?**

A **design pattern** is a **reusable solution** to a **common software design problem**. It’s not a finished code you can copy-paste; it’s a **template or guideline** to solve a particular type of problem in software design.

Think of it like this:

* When building a house, you don’t design a staircase from scratch every time. You follow a standard design that works.
* Similarly, in programming, design patterns are **proven ways to structure code** for maintainability, flexibility, and scalability.

---

### **Why Use Design Patterns?**

1. **Reusability:** Solve recurring problems without reinventing the wheel.
2. **Maintainability:** Code is easier to understand and change.
3. **Scalability:** Helps your code adapt to new requirements.
4. **Communication:** Patterns provide a common vocabulary for developers.

---

### **Categories of Design Patterns**

Design patterns are generally classified into **three main types**:

1. **Creational Patterns** – Focus on **object creation**.
   Example: Instead of calling `MyClass()` everywhere, patterns control object creation.

   * **Singleton:** Only one instance of a class exists.
   * **Factory:** A class that creates objects without exposing the creation logic.

2. **Structural Patterns** – Focus on **how classes and objects are composed**.
   Example: Combine simple objects to form complex ones.

   * **Adapter:** Makes incompatible interfaces compatible.
   * **Decorator:** Add behavior to objects dynamically.

3. **Behavioral Patterns** – Focus on **communication between objects**.
   Example: Manage how objects interact and distribute responsibilities.

   * **Observer:** Objects are notified of changes automatically.
   * **Strategy:** Choose algorithms at runtime.

---

### **Python Perspective**

Python’s dynamic and flexible nature makes many patterns **simpler** than in static languages like Java or C++. For example:

* Singleton in Python can be done with a **module** (since modules are loaded once).
* Decorators in Python are a natural implementation of the **Decorator pattern**.

So, learning design patterns in Python not only teaches you **structured programming**, but also **Pythonic ways** to implement them.

---

## **1. Singleton Pattern** (Creational)

**Goal:** Ensure a class has only **one instance**.

```python
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

a = Singleton()
b = Singleton()
print(a is b)  # True
```

---

## **2. Factory Pattern** (Creational)

**Goal:** Create objects **without specifying the exact class**.

```python
class Dog:
    def speak(self):
        return "Woof!"

class Cat:
    def speak(self):
        return "Meow!"

def animal_factory(animal_type):
    if animal_type == "dog":
        return Dog()
    elif animal_type == "cat":
        return Cat()

pet = animal_factory("dog")
print(pet.speak())  # Woof!
```

---

## **3. Builder Pattern** (Creational)

**Goal:** Separate object construction from its representation.

```python
class Pizza:
    def __init__(self):
        self.toppings = []

    def add_topping(self, topping):
        self.toppings.append(topping)
        return self

pizza = Pizza().add_topping("cheese").add_topping("tomato")
print(pizza.toppings)  # ['cheese', 'tomato']
```

---

## **4. Adapter Pattern** (Structural)

**Goal:** Make **incompatible interfaces compatible**.

```python
class EnglishSpeaker:
    def greet(self):
        return "Hello"

class FrenchSpeaker:
    def greet_in_french(self):
        return "Bonjour"

class FrenchAdapter:
    def __init__(self, french_speaker):
        self.french_speaker = french_speaker

    def greet(self):
        return self.french_speaker.greet_in_french()

french = FrenchAdapter(FrenchSpeaker())
print(french.greet())  # Bonjour
```

---

## **5. Decorator Pattern** (Structural / Pythonic)

**Goal:** **Add behavior** to objects **dynamically**.

```python
def bold(func):
    def wrapper():
        return "<b>" + func() + "</b>"
    return wrapper

@bold
def say_hello():
    return "Hello"

print(say_hello())  # <b>Hello</b>
```

---

## **6. Proxy Pattern** (Structural)

**Goal:** Control access to an object.

```python
class RealImage:
    def __init__(self, filename):
        self.filename = filename
        self.load()

    def load(self):
        print(f"Loading {self.filename}")

class ProxyImage:
    def __init__(self, filename):
        self.real_image = None
        self.filename = filename

    def display(self):
        if not self.real_image:
            self.real_image = RealImage(self.filename)
        print(f"Displaying {self.filename}")

image = ProxyImage("photo.jpg")
image.display()  # Loads and displays
image.display()  # Only displays
```

---

## **7. Observer Pattern** (Behavioral)

**Goal:** Objects **subscribe to events** and get notified automatically.

```python
class Subject:
    def __init__(self):
        self.observers = []

    def subscribe(self, observer):
        self.observers.append(observer)

    def notify(self, message):
        for observer in self.observers:
            observer.update(message)

class Observer:
    def __init__(self, name):
        self.name = name

    def update(self, message):
        print(f"{self.name} received: {message}")

subject = Subject()
subject.subscribe(Observer("Alice"))
subject.subscribe(Observer("Bob"))
subject.notify("Hello Observers!")
```

---

## **8. Strategy Pattern** (Behavioral)

**Goal:** Choose **algorithm at runtime**.

```python
class Context:
    def __init__(self, strategy):
        self.strategy = strategy

    def execute(self, a, b):
        return self.strategy(a, b)

def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

context = Context(add)
print(context.execute(5, 3))  # 8

context.strategy = multiply
print(context.execute(5, 3))  # 15
```

---

## **9. Command Pattern** (Behavioral)

**Goal:** Encapsulate a request as an object.

```python
class Light:
    def on(self):
        print("Light is ON")
    def off(self):
        print("Light is OFF")

class Command:
    def __init__(self, obj, action):
        self.obj = obj
        self.action = action

    def execute(self):
        getattr(self.obj, self.action)()

light = Light()
switch_on = Command(light, "on")
switch_off = Command(light, "off")

switch_on.execute()  # Light is ON
switch_off.execute()  # Light is OFF
```

---

## **10. Template Method Pattern** (Behavioral)

**Goal:** Define **skeleton of an algorithm**, letting subclasses **override steps**.

```python
class Game:
    def play(self):
        self.start()
        self.play_game()
        self.end()

    def start(self):
        print("Game started")

    def play_game(self):
        pass

    def end(self):
        print("Game ended")

class Chess(Game):
    def play_game(self):
        print("Playing Chess...")

chess = Chess()
chess.play()
# Game started
# Playing Chess...
# Game ended
```

---

✅ **Tip:** In Python, many patterns are simpler because of:

* **First-class functions** → Strategy, Command
* **Decorators** → Decorator pattern
* **Dynamic typing** → Adapter, Proxy
* **Modules** → Singleton

---