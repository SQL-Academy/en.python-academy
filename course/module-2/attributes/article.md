# Attributes and Methods in Python

In this article, we'll take a deep dive into attributes and methods — the most important components of classes in Python. They define what data objects store and what actions they can perform. Understanding different types of attributes and methods will allow you to create more flexible and powerful classes! 🔧

## Types of Attributes in Python

In Python, there are two main types of attributes:

1. **Instance attributes** — belong to a specific object (instance of a class)
2. **Class attributes** — belong to the entire class and are shared between all its instances

Let's look at each type in more detail:

### Instance Attributes

> Instance attributes are variables that store data unique to each specific object. They are defined inside methods (usually in `__init__`) and accessible through `self`.

```python
class Person:
    def __init__(self, name, age):
        # Instance attributes
        self.name = name
        self.age = age

    def birthday(self):
        self.age += 1
        return f"{self.name} is now {self.age} years old!"

# Creating two different objects
person1 = Person("Anna", 25)
person2 = Person("Ivan", 30)

# Each object has its own attribute values
print(f"{person1.name}: {person1.age} years old")

# Changing an attribute of one object doesn't affect another
print(person1.birthday())
print(f"{person2.name}: {person2.age} years old")  # Age hasn't changed
```

### Class Attributes

> Class attributes are variables that are defined at the class level and shared among all instances. They are defined directly inside the class but outside any methods.

```python
class Student:
    # Class attribute - common to all instances
    school = "School #1"

    def __init__(self, name):
        # Instance attribute - unique to each student
        self.name = name

# Creating students
student1 = Student("Alex")
student2 = Student("Kate")

# Displaying information about students
print(f"{student1.name}, {student1.school}")

# Changing the class attribute - this will affect all instances
Student.school = "Gymnasium #5"
print(f"{student1.name}, {student1.school}")
print(f"{student2.name}, {student2.school}")
```

Note that when changing the class attribute (`Student.school`), it affects all instances at once.

### When to Use Class Attributes vs. Instance Attributes

-   **Instance attributes** should be used for data that varies between objects (name, age, ID, etc.)
-   **Class attributes** should be used for:
    -   Constants or default settings for all instances
    -   Data that should be shared among all instances
    -   Tracking data related to the entire class (e.g., number of instances created)

## Types of Methods in Python

In Python, there are several types of methods:

1. **Regular methods** (instance methods) — work with a specific object
2. **Class methods** — work with the class as a whole
3. **Static methods** — work with neither the class nor an instance
4. **Special methods** — have special meaning in Python

### Regular Methods (Instance Methods)

> Regular methods are functions defined inside a class that take `self` as their first parameter. They can access instance attributes and call other instance methods.

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

# Creating a rectangle and calling methods
rect = Rectangle(5, 3)
print(f"Area: {rect.area()}")
print(f"Perimeter: {rect.perimeter()}")
```

### Class Methods

> Class methods are methods that take the class (not an instance) as their first parameter, usually called `cls`. They are decorated with `@classmethod` and typically used to create alternative constructors or to work with class attributes.

```python
class Date:
    def __init__(self, day, month, year):
        self.day = day
        self.month = month
        self.year = year

    def display(self):
        return f"{self.day:02d}.{self.month:02d}.{self.year}"

    # Class method - alternative constructor
    @classmethod
    def from_string(cls, date_string):
        day, month, year = map(int, date_string.split('.'))
        return cls(day, month, year)

# Creating an object the standard way
date1 = Date(15, 6, 2023)
print(date1.display())

# Creating an object using a class method
date2 = Date.from_string("25.12.2023")
print(date2.display())
```

### Static Methods

> Static methods are methods that take neither `self` nor `cls`. They are decorated with `@staticmethod` and typically used for utility functions that are logically related to the class but don't need access to class or instance state.

```python
class MathUtils:
    @staticmethod
    def is_prime(number):
        """Checks if a number is prime"""
        if number < 2:
            return False
        for i in range(2, int(number**0.5) + 1):
            if number % i == 0:
                return False
        return True

# Calling a static method through the class name
print(f"Is 7 a prime number: {MathUtils.is_prime(7)}")
print(f"Is 10 a prime number: {MathUtils.is_prime(10)}")
```

### Special Methods

> Special methods are methods with names that start and end with double underscores (e.g., `__init__`). They have special meaning in Python and are often used for operator overloading or defining standard behavior of objects.

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    # String representation
    def __str__(self):
        return f"Vector({self.x}, {self.y})"

    # Addition operator overloading
    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    # Multiplication operator overloading (by scalar)
    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)

# Creating vectors and using overloaded operators
v1 = Vector(3, 4)
v2 = Vector(1, 2)

print(f"v1 = {v1}")  # calls __str__
print(f"v1 + v2 = {v1 + v2}")  # calls __add__
print(f"v1 * 2 = {v1 * 2}")  # calls __mul__
```

## Commonly Used Special Methods

Here are some of the most commonly used special methods in Python:

| Method                          | Description                          | Usage Example      |
| ------------------------------- | ------------------------------------ | ------------------ |
| `__init__(self, ...)`           | Constructor                          | `obj = MyClass()`  |
| `__str__(self)`                 | String representation for users      | `print(obj)`       |
| `__repr__(self)`                | String representation for developers | `repr(obj)`        |
| `__len__(self)`                 | Object length                        | `len(obj)`         |
| `__getitem__(self, key)`        | Access by key/index                  | `obj[key]`         |
| `__setitem__(self, key, value)` | Assignment by key/index              | `obj[key] = value` |
| `__call__(self, ...)`           | Calling an object as a function      | `obj()`            |
| `__add__(self, other)`          | + operator                           | `obj + other`      |
| `__sub__(self, other)`          | - operator                           | `obj - other`      |
| `__eq__(self, other)`           | == operator                          | `obj == other`     |
| `__lt__(self, other)`           | < operator                           | `obj < other`      |

## Understanding Check

**Which decorator is used to create class methods in Python?**


We've explored the different types of attributes and methods in Python and now know when to use them.

In the next article, we'll dive into inheritance — another fundamental principle of OOP that allows us to create class hierarchies. See you then! 👋
