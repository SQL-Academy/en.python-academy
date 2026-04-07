# Classes and Objects

In this article, we'll explore in more detail the basic building blocks of object-oriented programming — classes and objects.
These concepts form the foundation of OOP and will help you structure your code in the most effective way. 🧱

## What is a Class?

> A class is a template or "blueprint" that defines the structure and behavior of objects of a specific type. A class describes what data (attributes) the objects will contain and what operations (methods) they can perform.

To continue with the construction analogy, a class is like a house plan, and an object is a specific house built according to that plan.

## Syntax for Creating a Class

In Python, a class is created using the `class` keyword:

```python
class Person:
    """This is the documentation for the Person class.
    The class represents a person with a name and age."""

    # Class constructor
    def __init__(self, name, age):
        self.name = name  # instance attribute
        self.age = age    # instance attribute

    # Class method
    def greet(self):
        return f"Hello! My name is {self.name}, I am {self.age} years old."

# Let's look at the class documentation
print(Person.__doc__)
```

The main elements defined in a class:

1. **Class name** — the name should follow the PascalCase convention (each word capitalized, no underscores)
2. **Documentation** — a documentation string describing the purpose of the class
3. **Constructor** — a special `__init__` method that's called when creating a new instance
4. **Attributes** — variables that store class or instance data
5. **Methods** — functions defined within the class that can work with class attributes

## What is an Object?

> An object (or class instance) is a concrete implementation of a class, created in memory. Each object has its own unique state (attribute values) and can perform the behaviors defined in the class (methods).

Objects are created by calling the class as if it were a function:

```python
# Creating Person class objects
person1 = Person("Anna", 25)
person2 = Person("Ivan", 30)

# Using object methods
print(person1.greet())
print(person2.greet())

# Accessing object attributes
print(f"Second person's name: {person2.name}")
print(f"First person's age: {person1.age}")
```

Note:

-   Each object has its own state (attribute values)
-   All objects of the same class have the same set of methods
-   Changing the attributes of one object does not affect other objects

## Understanding the self Parameter

You probably noticed the `self` parameter in the class method definitions. This is a special parameter that refers to the specific instance of the class.

-   `self` is automatically passed when a method is called via an object
-   Through `self` we get access to the attributes and other methods of the instance
-   While the name `self` is not mandatory, it's a common convention in Python

```python
class Demo:
    def method1(self):
        return "Method 1 called"

    def method2(self):
        # Calling another method via self
        return f"Method 2 called and inside it: {self.method1()}"

demo = Demo()
print(demo.method2())
```

## Modifying Objects After Creation

Objects can be modified after they're created, adding new attributes or changing existing ones:

```python
class Student:
    def __init__(self, name):
        self.name = name
        self.grades = []  # empty list of grades

    def add_grade(self, grade):
        self.grades.append(grade)
        return f"Added grade: {grade}"

    def average_grade(self):
        if not self.grades:
            return "No grades"
        return sum(self.grades) / len(self.grades)

# Creating a student
student = Student("Maria")
print(f"Created student: {student.name}")
print(f"Average grade: {student.average_grade()}")

# Adding grades
print(student.add_grade(5))
print(student.add_grade(4))
print(student.add_grade(5))

# Checking the average grade
print(f"Average grade after adding grades: {student.average_grade()}")

# Adding a new attribute (not recommended, but possible)
student.age = 19
print(f"Added age attribute: {student.age}")
```

## How to Properly Organize Classes?

When creating classes, follow these recommendations:

1. **Single Responsibility Principle** — a class should have only one reason to change
2. **Encapsulation** — try to hide internal implementation details
3. **Consistent Interface** — class methods should provide a clear, understandable interface
4. **Documentation** — add documentation to all classes and methods
5. **Reasonable Size** — a class should not be too large, it's better to split it into several classes

## Understanding Check

**Which of the following correctly describes the relationship between classes and objects?**


Now you know what classes and objects are in Python, and how to create and use them.

In the next article, we'll look at class attributes and methods in more detail, including different types of attributes (instance and class) and special Python methods. See you then! 👋
