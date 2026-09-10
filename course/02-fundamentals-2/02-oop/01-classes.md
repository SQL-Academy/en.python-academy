---
meta:
    title: "Classes and Objects"
    description: "Building your first class step by step: __init__, self and methods. Then deeper: how self works, object mutability and dynamic attributes."
---

# Classes and Objects

In the previous chapter we looked at a class from a distance: the shape, the template, the objects. Now let's build such a class ourselves, line by line.

## Building a class step by step

The shortest possible class in Python looks like this:

```python
class Person:
    pass

person = Person()
print(type(person))
<output>
<class '__main__.Person'>
</output>
```

`class Person:` declares the class, and `Person()` creates an object from it. An empty one, though: there is no data inside yet.

The data is set up in `__init__`, a special method that Python calls itself on every `Person(...)`. The call's arguments land in its parameters:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

person = Person("Anna", 25)
print(person.name)
<output>
Anna
</output>
print(person.age)
<output>
25
</output>
```

The line `self.name = name` reads: "take the parameter `name` and store it in the object under the name `name`". On the left is the object's attribute, on the right the method's parameter.

This is the template-filling from the diagram in the previous chapter: the call `Person("Anna", 25)` filled in the blanks.

The data is there, so let's add the action. A method is declared like an ordinary function, only inside the class, and it always takes `self` as its first parameter:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hi, my name is {self.name}, I am {self.age} years old."

person = Person("Anna", 25)
print(person.greet())
<output>
Hi, my name is Anna, I am 25 years old.
</output>
```

Spotted the oddity? `def greet(self)` declares a parameter, yet `person.greet()` passes nothing. So where does `self` come from?

## How self works

The `greet` method is written in the class once, while there can be any number of objects. So on a call the method somehow needs to know **whose** `name` to print. That's the dot's job: Python executes the call `person.greet()` as `Person.greet(person)` — the object to the left of the dot becomes the first argument itself. That is what arrives in the `self` parameter.

`person.greet()` — Python executes as → `Person.greet(person)`; the object to the left of the dot becomes the first argument and arrives in `self`.

Let's check that this is literally true:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hi, my name is {self.name}, I am {self.age} years old."

person = Person("Anna", 25)

# These two calls do the same thing:
print(person.greet())
<output>
Hi, my name is Anna, I am 25 years old.
</output>
print(Person.greet(person))
<output>
Hi, my name is Anna, I am 25 years old.
</output>
```

Since `self` is an ordinary first parameter, it could have any name: `def greet(this):` works too. But the whole Python community writes `self`, and linters complain about anything else.

Through `self`, methods can call other methods of the same object:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def is_adult(self):
        return self.age >= 18

    def describe(self):
        status = "adult" if self.is_adult() else "minor"
        return f"{self.name}: {status}"

person = Person("Anna", 25)
print(person.describe())
<output>
Anna: adult
</output>
```

## Objects in Python are mutable

So far the methods have only read the data: `is_adult` looked at `self.age` and changed nothing. But an object's state can also be changed: with a method, like `add_grade` below, or by assigning to an attribute directly.

```python
class Student:
    def __init__(self, name):
        self.name = name
        self.grades = []

    def add_grade(self, grade):
        self.grades.append(grade)
        return f"Grade added: {grade}"

    def average_grade(self):
        if not self.grades:
            return "No grades"
        return round(sum(self.grades) / len(self.grades), 1)

student = Student("Maria")
print(f"Average grade: {student.average_grade()}")
<output>
Average grade: No grades
</output>

print(student.add_grade(5))
<output>
Grade added: 5
</output>
print(student.add_grade(4))
<output>
Grade added: 4
</output>
print(student.add_grade(5))
<output>
Grade added: 5
</output>

print(f"Average grade: {student.average_grade()}")
<output>
Average grade: 4.7
</output>
```

The `add_grade` method changes `self.grades`, the list stored in the object. The changes happen **in place**: the next `student.average_grade()` call sees the updated state. It's not "return a new list", it's "change the existing one".

## Dynamic attributes

Changing existing attributes is business as usual. Python allows more: you can add **any** new attribute to an object at any moment, even one not declared in `__init__`. Let's check on a slimmed-down `Student` that has only a name:

```python
class Student:
    def __init__(self, name):
        self.name = name

student = Student("Maria")
student.age = 19            # added a new attribute on the fly
student.favorite_color = "blue"

print(student.age)
<output>
19
</output>
print(student.favorite_color)
<output>
blue
</output>
```

Technically this works, but real code almost never does it, and here is why:

- **The object's state becomes unpredictable.** Looking at the `Student` class, you can't tell which attributes an object actually has.
- **IDEs and linters can't help** with autocompletion: they only know what's declared in `__init__`.
- **A typo silently creates a new attribute** instead of a clear error. Write `student.aeg = 19` instead of `student.age = 19`, and Python quietly creates a new `aeg` field — a hard bug to find.

So declare all of an object's attributes in `__init__` — even with a `None` value if they get filled in later:

```python
class Student:
    def __init__(self, name):
        self.name = name
        self.age = None      # will be filled in later
        self.grades = []
```

That way the class honestly describes which fields an object has, and typos immediately turn into an `AttributeError`.

## Understanding check

**What happens when `Person.greet(person)` runs, if `greet` is defined with `self` as the first parameter?**

1. An error: you can't call a method through the class name. — Calling a method through the class name is allowed. You just pass the object as the first argument yourself.

2. **Correct answer:** The same as person.greet() — person becomes self. — Python executes person.greet() as Person.greet(person): the object to the left of the dot is passed as the first argument itself and arrives in self.

3. The method runs, but self is None. — self receives exactly what was passed as the first argument — in this case the person object.

4. A new Person object is created. — Objects are created only through Person() (the constructor call), not through a method call.

In the next article we'll look at attributes in detail: how instance attributes differ from class attributes, and how to avoid the classic trap with mutable default values in `__init__`.
