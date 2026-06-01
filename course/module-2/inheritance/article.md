# Inheritance in Python

In earlier lessons we built a `Person` class — name, age, a `greet()` method. Now we need a `Student` class. A student has a name and age (same as a person), can `greet()` (students also introduce themselves), but also has a school and grades, and greets in their own way — mentioning their school.

We could copy all the `Person` code into `Student` and tack on the new bits. But if we ever fix something in `Person`, the copy won't get the update. Duplicated code is a delayed bug.

Inheritance lets us say: "`Student` **is a** `Person`, plus a few extras." Don't copy — **extend** the existing class.

## Creating a child class

To inherit from another class, you put the parent's name in parentheses after the child class name:

```python-executable
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hi, my name is {self.name}, I'm {self.age} years old."

class Student(Person):   # Student inherits from Person
    pass                  # nothing new yet

# Create a student
student = Student("Anna", 20)

# greet() and attributes come from Person
print(student.name)
# Output: Anna
print(student.greet())
# Output: Hi, my name is Anna, I'm 20 years old.
```

We haven't written a single line inside `Student`, yet it works — because it inherited `__init__` and `greet()` from `Person`. That's inheritance at its simplest.

## Adding new attributes with super()

Now let's give the student a school and grades. We need to extend `__init__`: accept the old parameters (`name`, `age`) and the new ones (`school`, `grades`). To avoid duplicating the `self.name`/`self.age` setup, we'll call the parent's `__init__` via `super()`:

```python-executable
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
        self.grades = []

    def add_grade(self, grade):
        self.grades.append(grade)

student = Student("Anna", 20, "MIT")
student.add_grade(5)
student.add_grade(4)

print(student.greet())            # inherited method
# Output: Hi, my name is Anna, I'm 20 years old.
print(student.school, student.grades)
# Output: MIT [5, 4]
```

`super()` is a reference to "the parent of the current class". `super().__init__(name, age)` means "call `Person`'s `__init__` with `name` and `age`". We reuse the parent's logic instead of copying it.

## Overriding methods

A child class can **override** a method from its parent — define its own behavior under the same name. If `Student` has its own `greet()`, Python calls that one, not the parent's:

```python-executable
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
# Output: Hi, my name is Ivan.
print(student.greet())
# Output: Hi, my name is Anna. I study at MIT.
```

Inside an overridden method you can still call `super().greet()` to reuse the parent's logic rather than repeat it.

## Multi-level hierarchies

Inheritance can chain: `Student` extends `Person`, and `GraduateStudent` extends `Student`. The descendant has access to everything above it in the chain:

```python-executable
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
# Output: Hi, I'm Anna.
print(grad.school, "/", grad.advisor)
# Output: MIT / Prof. Smith
```

When Python looks up a method or attribute, it walks the chain: current class first, then the parent, then the parent's parent — until it finds something.

## Type checks: isinstance and issubclass

`isinstance(obj, Class)` checks whether an object is an instance of a class (or any of its subclasses). `issubclass(A, B)` checks whether class `A` descends from `B`:

```python-executable
class Person:
    pass

class Student(Person):
    pass

student = Student()

# A Student is a kind of Person
print(isinstance(student, Student))
# Output: True
print(isinstance(student, Person))
# Output: True

# Class-to-class relationships
print(issubclass(Student, Person))
# Output: True
print(issubclass(Person, Student))
# Output: False
```

The key idea: a student **is** a person (`isinstance(student, Person)` is `True`), but a person is not necessarily a student. Inheritance defines an "is-a" relationship that only goes one way.

## What about multiple inheritance?

Python lets a class have multiple parents: `class Duck(Flying, Swimming):`. It works, but the method lookup order in such hierarchies quickly becomes non-obvious (there's a separate algorithm called Method Resolution Order, MRO). In practice, single inheritance covers the vast majority of real code and is easier to read. Multiple inheritance is something to learn later, in an advanced course.

## What's next?

In the next lesson we'll tackle the second principle of OOP — encapsulation: how to hide a class's internals behind an interface and why that makes code more resilient to change.

---

**What happens when a method is called from a child class that doesn't override that method of the parent class?**

