# Polymorphism

Imagine you have a universal remote control that magically adapts to any device. 🪄 Press the same button — the TV will show a movie, the music center will play music, and the smart home will dim the lights. This is polymorphism in the programming world — the ability to use the same commands for different objects, getting different but appropriate results.

## What is Polymorphism?

> Polymorphism is the ability of objects of different classes to respond to the same methods or operations in different ways. In Greek, "polymorphism" means "many forms."

In simple terms: **same method, different behavior**, depending on the object.

### Real-life Example

Imagine the "Play" button ▶️ on various devices:

-   On a music player, it plays audio 🎵
-   On a video player, it starts a video 🎬
-   On a game console, it launches a game 🎮

The same button (common interface), but different behavior depending on the device — that's polymorphism.

## Types of Polymorphism in Python

In Python, there are several types of polymorphism:

1. **Polymorphism with functions and methods** — one function works with different data types
2. **Polymorphism with inheritance** — subclasses override methods of the parent class
3. **Polymorphism through "duck typing"** — if an object can do what we need, it doesn't matter what type it is
4. **Operator polymorphism** — operators (+, -, \* etc.) work differently with different types

Let's explore each type with simple examples!

## Polymorphism with Functions and Methods

Many built-in Python functions are already polymorphic — they work the same way with different data types:

```python
# The len() function works with different data types
print(f"String length: {len('Hello')}")  # Counts characters
print(f"List length: {len([1, 2, 3, 4])}")  # Counts elements
print(f"Dictionary length: {len({'a': 1, 'b': 2})}")  # Counts key-value pairs

# The + operator is also polymorphic!
print(f"10 + 5 = {10 + 5}")  # Adding numbers
print(f"'Hello' + ' World' = {'Hello' + ' World'}")  # Concatenating strings
print(f"[1, 2] + [3, 4] = {[1, 2] + [3, 4]}")  # Combining lists
```

See? The `len()` function and the `+` operator work completely differently depending on the data type, but are used in the same way!

## Polymorphism with Inheritance

This type of polymorphism is most commonly used in OOP. Let's imagine we have different animals that make different sounds:

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        # Base method that will be overridden in subclasses
        raise NotImplementedError("Each animal must define its own sound!")

    def introduce(self):
        # This method uses speak(), but doesn't know how it's implemented
        return f"My name is {self.name}, and I say {self.speak()}"

class Dog(Animal):
    def speak(self):
        return "woof!"  # Dogs bark

class Cat(Animal):
    def speak(self):
        return "meow!"  # Cats meow

# Create animals and call the same method
dog = Dog("Rex")
cat = Cat("Fluffy")

print(dog.introduce())
print(cat.introduce())

# Polymorphism in action - processing different objects the same way
# We can work with a list of animals without knowing what they are!
animals = [Dog("Buddy"), Cat("Whiskers"), Dog("Max")]
print("Animals introducing themselves:")
for animal in animals:
    print(f"- {animal.introduce()}")
```

What's happening here:

1. We have a base class `Animal` with a `speak()` method that needs to be overridden
2. The `Dog` and `Cat` classes inherit from `Animal` and override the `speak()` method
3. The `introduce()` method is defined in the base class and uses polymorphism: it calls `speak()` without knowing its specific implementation
4. We can work with different animals through a single interface, without worrying about the specific type

## Polymorphism through Abstract Classes

Abstract classes help us define which methods all subclasses must have. They're like a blueprint or contract:

```python
from abc import ABC, abstractmethod  # ABC = Abstract Base Class

class Shape(ABC):  # Abstract class for geometric shapes
    @abstractmethod  # This decorator means "method must be implemented in subclasses"
    def area(self):
        pass  # No implementation here, but it must exist in all subclasses

    @abstractmethod
    def perimeter(self):
        pass

    def describe(self):
        # This method uses area() and perimeter(), without knowing their implementation
        return f"Area: {self.area():.2f}, Perimeter: {self.perimeter():.2f}"

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height  # Rectangle area

    def perimeter(self):
        return 2 * (self.width + self.height)  # Rectangle perimeter

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        import math
        return math.pi * self.radius ** 2  # Circle area

    def perimeter(self):
        import math
        return 2 * math.pi * self.radius  # Circumference

# Work with different shapes in the same way
shapes = [Rectangle(5, 3), Circle(4)]
for i, shape in enumerate(shapes, 1):
    print(f"Shape {i}: {shape.describe()}")
```

Benefits of abstract classes:

-   They ensure that subclasses implement all necessary methods
-   Help structure code and make it more predictable
-   Allow using polymorphism more safely

## "Duck Typing"

In Python, there's an interesting principle: "If it walks like a duck and quacks like a duck, then it probably is a duck." This means that what matters is not the types of objects, but what they can do:

```python
class Duck:
    def swim(self):
        return "Duck swims"

    def sound(self):
        return "Quack quack!"

class Person:
    def swim(self):
        return "Person swims"

    def sound(self):
        return "Hello!"

# This function works with ANY object that has swim() and sound() methods
# It doesn't matter what class it is!
def make_it_swim_and_sound(entity):
    print(f"Swimming: {entity.swim()}")
    print(f"Sound: {entity.sound()}")

# Duck and Person are completely different classes, but the function works with both
duck = Duck()
person = Person()

print("Duck:")
make_it_swim_and_sound(duck)

print("\nPerson:")
make_it_swim_and_sound(person)
```

In this example:

-   We don't have a common base class
-   Duck and Person are completely different classes
-   But both can "swim" and "make a sound"
-   Our function works with any object that has `swim()` and `sound()` methods

This is "duck typing" - a more flexible form of polymorphism inherent to Python.

## Understanding Check

**Which of the following is an example of polymorphism in Python?**


## Advantages of Polymorphism

Why you should use polymorphism in your code:

1. **Code simplification** — write one method instead of several similar ones
2. **Flexibility** — easily add new types without changing existing code
3. **Readability** — code becomes clearer and easier to maintain
4. **Extensibility** — new classes work with old code without changes
5. **Less repetition** — common logic is written only once
