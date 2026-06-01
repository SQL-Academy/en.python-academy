# Attributes and Methods in Python

In the previous lesson we saw attributes (`self.name`) and methods (`def greet(self)`). Now let's go deeper: there are also **class attributes** (shared by every instance), methods split into three kinds (regular, class, static), and a classic mutable-default pitfall hiding inside `__init__` that catches even experienced developers.

## Types of attributes

1. **Instance attributes**: unique to each object.
2. **Class attributes**: shared by all instances.

### Instance attributes

> Instance attributes: variables that store data unique to each object. Usually created in `__init__` via `self.name = ...`.

```python-executable
class Person:
    def __init__(self, name, age):
        # Instance attributes
        self.name = name
        self.age = age

    def birthday(self):
        self.age += 1
        return f"{self.name} is now {self.age} years old!"

# Create two different objects
person1 = Person("Anna", 25)
person2 = Person("Ivan", 30)

# Each object has its own attribute values
print(f"{person1.name}: {person1.age} years old")
# Output: Anna: 25 years old

# Changing one object's attribute doesn't affect another
print(person1.birthday())
# Output: Anna is now 26 years old!
print(f"{person2.name}: {person2.age} years old")  # Age hasn't changed
# Output: Ivan: 30 years old
```

### Class attributes

> Class attributes: variables declared right inside the class body (outside any methods). They are shared by all instances.

```python-executable
class Student:
    # Class attribute: shared by all instances
    school = "School #1"

    def __init__(self, name):
        # Instance attribute: one per student
        self.name = name

# Create students
student1 = Student("Alex")
student2 = Student("Kate")

# Each has their own name; school is shared
print(f"{student1.name}, {student1.school}")
# Output: Alex, School #1

# Change the class attribute: every instance sees it immediately
Student.school = "Gymnasium #5"
print(f"{student1.name}, {student1.school}")
# Output: Alex, Gymnasium #5
print(f"{student2.name}, {student2.school}")
# Output: Kate, Gymnasium #5
```

### When to use which

-   **Instance attributes**: for data that varies between objects (name, age, id).
-   **Class attributes**: for:
    -   Constants and default values;
    -   Data shared by every instance (e.g. the school name across all students);
    -   Class-level counters (e.g. how many objects have been created).

## The mutable-default pitfall

A classic Python trap that catches even seasoned developers. Suppose we want a student to start with an empty list of grades. It seems natural to write:

```python-executable
class Student:
    def __init__(self, name, grades=[]):   # looks harmless
        self.name = name
        self.grades = grades

s1 = Student("Anna")
s1.grades.append(5)
print(s1.grades)
# Output: [5]

s2 = Student("Ivan")
print(s2.grades)   # we expect []
# Output: [5]
```

Ivan inherited Anna's grade even though we never added anything to his list. Why?

Default parameter values are evaluated **once**, when the function is defined, not on every call. The list `[]` is created once and reused by every call to `Student(...)` that doesn't pass a `grades` argument. So `s1.grades` and `s2.grades` point to **the same** list in memory.

The fix: default to `None`, and create the real list inside:

```python-executable
class Student:
    def __init__(self, name, grades=None):
        self.name = name
        self.grades = grades if grades is not None else []

s1 = Student("Anna")
s1.grades.append(5)
print(s1.grades)
# Output: [5]

s2 = Student("Ivan")
print(s2.grades)
# Output: []
```

Now each object gets its own fresh list. The same trick works for dicts, sets, and any other mutable default.

## Types of methods

In Python there are several kinds of methods:

1. **Regular methods** (instance methods): work with a specific object via `self`.
2. **Class methods**: work with the class as a whole, receive `cls`.
3. **Static methods**: receive neither `self` nor `cls`.
4. **Special methods**: have special meaning to Python (e.g. `__init__`, `__str__`).

### Regular methods

> Regular methods: functions inside a class that take `self` as their first parameter. Through `self` they access the object's attributes and call other methods.

```python-executable
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

# Create a rectangle and call methods
rect = Rectangle(5, 3)
print(f"Area: {rect.area()}")
# Output: Area: 15
print(f"Perimeter: {rect.perimeter()}")
# Output: Perimeter: 16
```

### Class methods

> Class methods: take the class itself as the first parameter (usually called `cls`), not an instance. Decorated with `@classmethod`. Often used as alternative constructors.

```python-executable
class Date:
    def __init__(self, day, month, year):
        self.day = day
        self.month = month
        self.year = year

    def display(self):
        return f"{self.day:02d}.{self.month:02d}.{self.year}"

    # Class method: alternative constructor
    @classmethod
    def from_string(cls, date_string):
        day, month, year = map(int, date_string.split('.'))
        return cls(day, month, year)

# Create the standard way
date1 = Date(15, 6, 2023)
print(date1.display())
# Output: 15.06.2023

# Create using a class method
date2 = Date.from_string("25.12.2023")
print(date2.display())
# Output: 25.12.2023
```

### Static methods

> Static methods: take neither `self` nor `cls`. Decorated with `@staticmethod`. Used for utility functions logically related to a class but not needing access to its state.

```python-executable
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

# Call a static method through the class name
print(f"Is 7 a prime number: {MathUtils.is_prime(7)}")
# Output: Is 7 a prime number: True
print(f"Is 10 a prime number: {MathUtils.is_prime(10)}")
# Output: Is 10 a prime number: False
```

### Special methods

> Special methods: methods whose names start and end with double underscores (`__init__`, `__str__`, `__add__`, `__eq__`). Python calls them automatically for specific operations: creating an object, printing, addition, comparison.

```python-executable
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    # String representation: called by print() and str()
    def __str__(self):
        return f"Vector({self.x}, {self.y})"

    # Overload + operator
    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    # Overload == operator
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

# Create vectors and use the overloaded operators
v1 = Vector(3, 4)
v2 = Vector(1, 2)
v3 = Vector(3, 4)

print(f"v1 = {v1}")          # calls __str__
# Output: v1 = Vector(3, 4)
print(f"v1 + v2 = {v1 + v2}")  # calls __add__
# Output: v1 + v2 = Vector(4, 6)
print(v1 == v2)              # calls __eq__
# Output: False
print(v1 == v3)              # calls __eq__
# Output: True
```

Without `__eq__`, comparing `v1 == v3` would have returned `False` even though their coordinates match: by default Python compares objects by identity (are they the same object in memory?), not by content. Defining `__eq__` lets us say: "treat these objects as equal when their coordinates match."

## Commonly used special methods

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

## What's next?

In the next lessons we'll go through the four principles of OOP one at a time. Starting with inheritance: how one class extends another, reusing its attributes and methods.

---

**Which decorator is used to create class methods in Python?**

