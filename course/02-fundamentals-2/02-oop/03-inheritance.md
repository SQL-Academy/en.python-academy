---
meta:
    title: "Inheritance in Python"
    description: "How one class extends another: child classes, super(), method overriding, and type checks with isinstance."
---

# Inheritance in Python

In earlier lessons we built a `Person` class — name, age, a `greet()` method. Now we need a `Student` class. A student has a name and age (same as a person), can `greet()` (students also introduce themselves), but also has a school, and greets in their own way — mentioning their school.

We could copy all the `Person` code into `Student` and tack on the new bits. But if we ever fix something in `Person`, the copy won't get the update. Duplicated code is a delayed bug.

Inheritance lets us say: "`Student` **is a** `Person`, plus a few extras." Don't copy — **extend** the existing class.

## Creating a child class

To inherit from another class, you put the parent's name in parentheses after the child class name:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hi, my name is {self.name}, I'm {self.age} years old."

class Student(Person):   # Student inherits from Person
    pass                  # nothing new yet

student = Student("Anna", 20)

# greet() and attributes come from Person
print(student.name)
<output>
Anna
</output>
print(student.greet())
<output>
Hi, my name is Anna, I'm 20 years old.
</output>
```

We haven't written a single line inside `Student`, yet it works — because it inherited `__init__` and `greet()` from `Person`. That's inheritance at its simplest.

`class Student(Person)` — inherits: `name`, `age`, `greet()` — from Person.

school is declared in Student, while name, age and greet() come from Person

## Adding new attributes with super()

Now let's give the student a school. We need to extend `__init__`: accept the old parameters (`name`, `age`) and the new one (`school`). To avoid duplicating the `self.name`/`self.age` setup, we'll call the parent's `__init__` via `super()`:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hi, my name is {self.name}, I'm {self.age} years old."

class Student(Person):
    def __init__(self, name, age, school):
        super().__init__(name, age)   # let Person set name and age
        self.school = school

student = Student("Anna", 20, "MIT")

print(student.greet())            # inherited method
<output>
Hi, my name is Anna, I'm 20 years old.
</output>
print(student.school)
<output>
MIT
</output>
```

`super()` is a way to reach the parent of the current class. The call in the example reads: "ask `Person` to run its `__init__`, passing `name` and `age`". We reuse the parent's logic instead of copying it.

## Overriding methods

A child class can **override** a method from its parent — define its own behavior under the same name. If `Student` has its own `greet()`, Python calls that one, not the parent's:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hi, my name is {self.name}."

class Student(Person):
    def __init__(self, name, age, school):
        super().__init__(name, age)
        self.school = school

    def greet(self):
        # Use the parent's greet() and add our own bit
        return f"{super().greet()} I study at {self.school}."

person = Person("Ivan", 30)
student = Student("Anna", 20, "MIT")

print(person.greet())
<output>
Hi, my name is Ivan.
</output>
print(student.greet())
<output>
Hi, my name is Anna. I study at MIT.
</output>
```

Inside an overridden method you can still call `super().greet()` to reuse the parent's logic rather than repeat it.

## Multi-level hierarchies

Inheritance can chain: `Student` extends `Person`, and `GraduateStudent` extends `Student`. The descendant has access to everything above it in the chain:

```python
class Person:
    def __init__(self, name):
        self.name = name

    def greet(self):
        return f"Hi, I'm {self.name}."

class Student(Person):
    def __init__(self, name, school):
        super().__init__(name)
        self.school = school

class GraduateStudent(Student):
    def __init__(self, name, school, advisor):
        super().__init__(name, school)
        self.advisor = advisor

grad = GraduateStudent("Anna", "MIT", "Prof. Smith")

# Method from Person, attributes from every level
print(grad.greet())
<output>
Hi, I'm Anna.
</output>
print(grad.school, "/", grad.advisor)
<output>
MIT / Prof. Smith
</output>
```

When Python looks up a method or attribute, it walks the chain: current class first, then the parent, then the parent's parent — until it finds something.

## Type check: isinstance

`isinstance(obj, Class)` checks whether an object is an instance of a class or any of its subclasses:

```python
class Person:
    pass

class Student(Person):
    pass

person = Person()
student = Student()

print(isinstance(student, Student))
<output>
True
</output>
print(isinstance(student, Person))
<output>
True
</output>
print(isinstance(person, Student))
<output>
False
</output>
```

The key idea: a student **is** a person, so `isinstance(student, Person)` gives `True`. The reverse does not hold: a person is not necessarily a student.

## Understanding check

**What happens when a method is called from a child class that doesn't override that method of the parent class?**

1. **Correct answer:** The parent class's method will be called — If the method isn't overridden in the child class, Python walks up the parent chain and runs the version it finds.

2. An AttributeError will be raised — If the parent has the method, Python will find and run it even though the child class doesn't define it explicitly.

3. Nothing happens, the method just does not run — Python walks the inheritance chain, and if it finds the method on the parent, it runs it.

4. The method returns None by default — The parent's version runs; None is not returned by default.

In the next lesson we'll tackle the second principle of OOP — encapsulation: how to hide a class's internals, exposing only what's needed, and why that makes code more resilient to change.
