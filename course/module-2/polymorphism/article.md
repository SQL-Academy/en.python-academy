# Polymorphism

Suppose we have a dog, a cat, and a duck, and we need to make each of them `speak()`. Without polymorphism it would look something like this:

```python
for animal in animals:
    if isinstance(animal, Dog):
        print(animal.bark())
    elif isinstance(animal, Cat):
        print(animal.meow())
    elif isinstance(animal, Duck):
        print(animal.quack())
```

Every time a new kind of animal shows up, you have to add another `elif` branch. Code that **knows** about every possible type can't grow without being rewritten.

Polymorphism is the idea of "make the same call, and let the class decide how to respond". The same loop becomes:

```python
for animal in animals:
    print(animal.speak())
```

`Dog.speak()` returns "Woof!", `Cat.speak()` returns "Meow!", `Duck.speak()` returns "Quack!". One call, different behavior depending on the object.

## Polymorphism through inheritance

The most common case: a common parent class defines the "contract" (which methods the children must have), and each child implements them its own way.

```python-executable
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return f"{self.name} is silent."

class Dog(Animal):
    def speak(self):
        return f"{self.name} says: Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says: Meow!"

class Duck(Animal):
    def speak(self):
        return f"{self.name} says: Quack!"

animals = [Dog("Rex"), Cat("Fluffy"), Duck("Donald")]
for animal in animals:
    print(animal.speak())
# Output:
# Rex says: Woof!
# Fluffy says: Meow!
# Donald says: Quack!
```

Inside the loop we never ask "what kind of object are you?" — each animal knows how to speak for itself. Add `class Cow(Animal):` with its own `speak()`, and the loop doesn't change by a single line.

## Abstract classes

Often you want to be sure that **every subclass actually implements the required method**. For example, the base `Shape` class should force every concrete class (Rectangle, Circle, …) to implement `area()`. If someone forgets, it's better to find out immediately, not when the program is already running.

For that Python has **abstract base classes** in the `abc` module:

```python-executable
from abc import ABC, abstractmethod
import math

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass

    def describe(self):
        # Uses area() and perimeter() without knowing how they're implemented
        return f"Area: {self.area():.2f}, perimeter: {self.perimeter():.2f}"

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return math.pi * self.radius ** 2

    def perimeter(self):
        return 2 * math.pi * self.radius

shapes = [Rectangle(5, 3), Circle(4)]
for i, shape in enumerate(shapes, 1):
    print(f"Shape {i}: {shape.describe()}")
# Output:
# Shape 1: Area: 15.00, perimeter: 16.00
# Shape 2: Area: 50.27, perimeter: 25.13
```

What `ABC` gave us:

-   You can't instantiate `Shape` directly (`Shape()` raises an error). And that's reasonable: "a shape" on its own is meaningless; you need a concrete one.
-   If `Circle` had forgotten to define `perimeter()`, Python would refuse to create a `Circle()` at creation time, not later when some missing method gets called.

Meanwhile `describe()` is defined right in `Shape` and works for any descendant — it relies on `area()` and `perimeter()` without knowing their implementations. That's polymorphism through an abstract base class.

## Duck typing

Python goes one step further: polymorphism **doesn't** require shared inheritance. If an object behaves the right way (has the right methods), it qualifies. People phrase it as: "if it walks like a duck and quacks like a duck, it's a duck".

```python-executable
class Duck:
    def swim(self):
        return "The duck swims."

    def sound(self):
        return "Quack quack!"

class Person:
    def swim(self):
        return "The person swims."

    def sound(self):
        return "Hello!"

def describe(entity):
    print(entity.swim())
    print(entity.sound())

describe(Duck())
# Output:
# The duck swims.
# Quack quack!
describe(Person())
# Output:
# The person swims.
# Hello!
```

The `describe` function doesn't check whether it's looking at a duck or a person. All it cares about is that the object has `swim()` and `sound()` methods. `Duck` and `Person` share no parent class, but polymorphism still works.

This is a very Pythonic approach: instead of "describe the type", "describe the behavior". In practice it often saves you from needless layers of abstraction.

## What's next?

Polymorphism wraps up the four principles of OOP. The key practical effect: code that calls methods through a shared interface doesn't need to be rewritten when new classes appear. Extensibility is built into the architecture.

---

**Which of the following is an example of polymorphism in Python?**

