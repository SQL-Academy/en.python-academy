# Inheritance in Python

> Inheritance is a mechanism that allows you to create a new class based on an existing one. The child class receives the attributes and methods of the parent class, but can extend and modify this functionality.

Simply put, inheritance represents an "is-a" relationship: a dog **is an** animal, a passenger car **is a** vehicle.

### Why is inheritance needed?

-   🔄 **Code reuse** — eliminates duplication
-   🌲 **Logical hierarchies** — reflects natural relationships between objects
-   🧩 **Extensibility** — easy to add new functionality
-   🔄 **Polymorphism** — use objects of different classes uniformly

## Simple Inheritance in Python

Creating a child class in Python is very simple — you need to specify the parent class in parentheses:

```python
# Base class
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "Animal sound"

# Child class
class Dog(Animal):  # Dog inherits from Animal
    def speak(self):  # Override the method
        return f"{self.name} says: Woof!"

# Create instances
animal = Animal("Creature")
dog = Dog("Rex")

# Call methods
print(animal.speak())
print(dog.speak())  # Calls the overridden method
print(dog.name)  # Attribute inherited from the parent class
```

## Calling Parent Class Methods with super()

Often you need to extend the functionality of a parent method rather than completely replace it. For this, the `super()` function is used:

```python
class Vehicle:
    def __init__(self, brand):
        self.brand = brand

    def info(self):
        return f"Vehicle brand {self.brand}"

class Car(Vehicle):
    def __init__(self, brand, model):
        super().__init__(brand)  # Call the parent's constructor
        self.model = model

    def info(self):
        # Extend the parent method
        return f"{super().info()}, model {self.model}"

# Create a car
car = Car("Toyota", "Corolla")
print(car.info())
```

## Inheritance Hierarchies

Inheritance can be arranged in multi-level hierarchies:

```python
class Animal:
    def eat(self):
        return "Animal eats"

class Mammal(Animal):
    def breathe(self):
        return "Breathes with lungs"

class Dog(Mammal):
    def bark(self):
        return "Woof!"

# Create a dog
dog = Dog()
print(dog.eat())    # Method from Animal
print(dog.breathe())  # Method from Mammal
print(dog.bark())   # Method from Dog
```

## Checking Inheritance: isinstance() and issubclass()

Python provides useful functions for checking inheritance relationships:

```python
# Base class - parent in the hierarchy
class Vehicle:
    pass  # Empty class for demonstrating inheritance

# Child classes - inherit from Vehicle
class Car(Vehicle):
    pass  # Car is a type of Vehicle

class Bicycle(Vehicle):
    pass  # Bicycle is also a type of Vehicle

# Create objects
car = Car()  # Instance of the Car class
bicycle = Bicycle()  # Instance of the Bicycle class

# Check object types
# Is car an instance of the Car class?
print(isinstance(car, Car))
# Is car an instance of the Vehicle class (through inheritance)?
print(isinstance(car, Vehicle))
# Is car an instance of the Bicycle class?
print(isinstance(car, Bicycle))

# Check relationships between classes
print(issubclass(Car, Vehicle))  # Is Car a subclass of Vehicle?
print(issubclass(Vehicle, Car))  # Is Vehicle a subclass of Car?
```

## Abstract Classes

Abstract classes serve as templates for creating other classes. They contain abstract methods that must be implemented in subclasses:

```python
from abc import ABC, abstractmethod

class Shape(ABC):  # Abstract class
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

# Attempt to create an instance of an abstract class
try:
    shape = Shape()
except TypeError as e:
    print(f"Error: {e}")

# Creating an instance of a concrete class
rect = Rectangle(5, 3)
print(f"Area: {rect.area()}")
print(f"Perimeter: {rect.perimeter()}")
```

## Multiple Inheritance

Python supports multiple inheritance, where a class inherits from several parents:

```python
class Flying:
    def fly(self):
        return "I can fly!"

class Swimming:
    def swim(self):
        return "I can swim!"

# Multiple inheritance
class Duck(Flying, Swimming):
    def sound(self):
        return "Quack!"

# Create a duck
duck = Duck()
print(duck.fly())
print(duck.swim())
print(duck.sound())
```

### Method Resolution Order (MRO)

With multiple inheritance, it's important to know in what order Python searches for methods in classes. This order is called Method Resolution Order (MRO):

```python
class A:
    def method(self):
        return "A"

class B(A):
    def method(self):
        return "B"

class C(A):
    def method(self):
        return "C"

class D(B, C):
    pass

# Check the MRO of class D
print(D.__mro__)

# Create an object and call the method
d = D()
print(d.method())  # The method from B will be called, as B comes before C
```

## Understanding Check

**What happens when a method is called from a child class that doesn't override this method of the parent class?**
