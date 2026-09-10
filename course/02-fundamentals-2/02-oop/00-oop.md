---
meta:
    title: "OOP in Python"
    description: "Introduction to object-oriented programming: what a class and an object are, and which four OOP principles the next chapters cover."
---

# OOP in Python

Programs often model things from the real world: a bank account, a user, a car. Every such thing has **data** (balance, name, speed) and **actions** (deposit money, introduce itself, accelerate).

> Object-oriented programming (OOP) is a way to describe the data and the actions together. The template for such a bundle is called a **class**, and a concrete thing created from the template is an **object**.

## First example

Let's describe a `Car` class and create two cars from it. Don't study the syntax yet: `class`, `__init__` and `self` are all covered step by step in the next chapter. What matters now is the shape: the data and the actions are described in one place.

```python
class Car:
    def __init__(self, make, model):
        self.make = make
        self.model = model
        self.is_running = False

    def start_engine(self):
        self.is_running = True
        return f"{self.make} {self.model}: engine started"

    def stop_engine(self):
        self.is_running = False
        return f"{self.make} {self.model}: engine stopped"

my_car = Car("Toyota", "Corolla")
another_car = Car("Honda", "Civic")

print(another_car.start_engine())
<output>
Honda Civic: engine started
</output>
print(my_car.is_running)
<output>
False
</output>
print(another_car.is_running)
<output>
True
</output>
```

We started the engine of the second car only, and only its `is_running` changed. That's the whole point: any number of objects can be created from one class, and each keeps its own data.

## Class and object

`class Person` → `object person1`, `object person2`.

The terms for all of this:

- `Car` — a **class**: the template objects are created from
- `my_car`, `another_car` — **objects** (instances) of that class
- `make`, `model`, `is_running` — **attributes**: the object's data
- `start_engine()`, `stop_engine()` — **methods**: the object's actions

## The four principles of OOP

OOP traditionally rests on four concepts. Each has its own lesson further on (abstraction comes up inside the polymorphism lesson), so for now one sentence about each will do:

1. **Encapsulation** — packing data and the methods that work with that data into one object. Outside code doesn't reach into the implementation details; it works through the class's interface.
2. **Inheritance** — creating a new class on top of an existing one, reusing its attributes and methods.
3. **Polymorphism** — working with objects of different classes through a single interface (calling `.area()` the same way on a circle, a square and a triangle).
4. **Abstraction** — keeping the essential characteristics of an object and hiding the unimportant details.

## Understanding check

**How is a class different from an object?**

1. **Correct answer:** A class is a template, and an object is a concrete thing created from it — Any number of objects can be created from one class, and each gets its own attribute values — just like my_car and another_car have their own makes.

2. Class and object are synonyms, there is no difference — There is a difference: the Car class in the example is one, while two cars were created from it, each with its own data.

3. An object is a template, and a class is created from an object — The other way round: the template is the class, and objects are created from it by calling Car(...).

4. A class holds the data, and an object holds only the methods — The data (attributes) is held by each object separately, while the class describes which attributes and methods every object will have.

The next chapter is "Classes": we'll go through all the syntax that flashed by here, step by step.
