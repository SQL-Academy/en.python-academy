# Python OOP

It's time to get acquainted with one of the most important programming paradigms — object-oriented programming (OOP).
This is a fundamental approach that will help you create more organized, reusable, and understandable code. 🧩

## What is OOP?

> Object-oriented programming is an approach to software development where a program is built as a collection of objects, each of which is an instance of a specific class, and classes form an inheritance hierarchy.

In simple terms: OOP allows us to model the real world in code by representing entities as "objects" with characteristics (data) and behavior (functions).

Imagine you're describing a car. It has:

-   Characteristics: color, make, year of manufacture, current speed
-   Behavior: start the engine, accelerate, brake, honk

In OOP, characteristics become object attributes, and behavior becomes methods.

## Basic Principles of OOP

There are four basic principles of OOP:

1. **Encapsulation** — combining data and methods that work with this data into a single object and hiding implementation details
2. **Inheritance** — creating new classes based on existing ones while preserving their properties and methods
3. **Polymorphism** — the ability to use objects with the same interfaces without information about the type and internal structure of the object
4. **Abstraction** — highlighting important characteristics of an object and ignoring non-essential details

We'll study each of these principles in separate articles, but for now, let's see what OOP looks like in Python.

## OOP Example in Python

Here's a simple example of a `Car` class with attributes and methods:

```python
class Car:
    def __init__(self, make, model):
        self.make = make
        self.model = model
        self.is_running = False  # engine started or not

    def start_engine(self):
        """Start the engine"""
        if not self.is_running:
            self.is_running = True
            return f"{self.make} {self.model}: Engine started"
        return f"{self.make} {self.model}: Engine was already running"

    def stop_engine(self):
        """Stop the engine"""
        if self.is_running:
            self.is_running = False
            return f"{self.make} {self.model}: Engine stopped"
        return f"{self.make} {self.model}: Engine was already stopped"

# Create an instance of the Car class
my_car = Car("Toyota", "Corolla")

# Use the object's methods
print(my_car.start_engine())
print(my_car.stop_engine())

# We can create another instance of the Car class
another_car = Car("Honda", "Civic")
print(another_car.start_engine())
```

In this example:

-   `Car` is a class that describes what a car is
-   `my_car` and `another_car` are instances (objects) of the class
-   `make`, `model`, `is_running` are attributes
-   `start_engine()`, `stop_engine()` are methods

## Benefits of OOP

OOP offers many benefits:

1. **Modularity** — code is divided into logical blocks (classes) that are easier to understand and maintain
2. **Reusability** — classes can be used repeatedly in different parts of the program or even in different projects
3. **Scalability** — it's easier to add new features without breaking existing functionality
4. **Complexity management** — complex systems are easier to model and understand

## Practical Example: Banking System

Let's create a simple model of a bank account:

```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance

    def deposit(self, amount):
        if amount > 0:
            self.balance += amount
            return f"Deposited ${amount}. New balance: ${self.balance}"
        return "Deposit amount must be positive"

    def withdraw(self, amount):
        if amount > 0:
            if amount <= self.balance:
                self.balance -= amount
                return f"Withdrew ${amount}. New balance: ${self.balance}"
            return "Insufficient funds"
        return "Withdrawal amount must be positive"

    def get_balance(self):
        return f"{self.owner}'s account balance: ${self.balance}"

# Create a bank account
account = BankAccount("John Smith", 1000)

# Use account methods
print(account.get_balance())
print(account.deposit(500))
print(account.withdraw(200))
print(account.withdraw(2000))  # Attempt to withdraw more than the account balance
```

This example shows how OOP can be used to model a real object (bank account) with its data (owner, balance) and operations (deposit, withdrawal, balance check).

## Understanding Check

**What are the main principles of OOP?**


## Conclusion

You've been introduced to the basics of OOP in Python.
Now you know about the four key principles: encapsulation, inheritance, polymorphism, and abstraction.

In the following articles, we'll explore each of these concepts in more detail.
