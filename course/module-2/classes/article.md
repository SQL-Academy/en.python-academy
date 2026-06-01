# Classes and Objects

In the previous lesson we covered the basics: a class is a template, an object is a filled-in instance, and methods take `self` as their first parameter. In this article we'll go deeper on three topics: how `self` **actually** works under the hood, the fact that objects in Python are **mutable**, and why you can add attributes **on the fly** (and why you usually shouldn't).

## How self works

When you write `person.greet()`, Python internally turns this into `Person.greet(person)`. The object on the left of the dot automatically becomes the first argument of the method — that's the `self` you see in the method signature.

Through `self`, the method sees its own data:

```python-executable
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hi, my name is {self.name}, I'm {self.age} years old."

person = Person("Anna", 25)

# These two calls do the same thing:
print(person.greet())
# Output: Hi, my name is Anna, I'm 25 years old.
print(Person.greet(person))
# Output: Hi, my name is Anna, I'm 25 years old.
```

`self` isn't a keyword or magic. It's just the conventional name for a method's first parameter. Technically you can call it whatever you want (`def greet(this):` works too), but the Python community expects `self` and linters will complain about other names.

Through `self`, methods can call other methods on the same object:

```python-executable
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def is_adult(self):
        return self.age >= 18

    def describe(self):
        status = "an adult" if self.is_adult() else "a minor"
        return f"{self.name}: {status}"

person = Person("Anna", 25)
print(person.describe())
# Output: Anna: an adult
```

## Objects in Python are mutable

After an object is created, its state can change: by calling methods that modify attributes, or by assigning to attributes directly.

```python-executable
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
        return sum(self.grades) / len(self.grades)

student = Student("Maria")
print(f"Average: {student.average_grade()}")
# Output: Average: No grades

print(student.add_grade(5))
# Output: Grade added: 5
print(student.add_grade(4))
# Output: Grade added: 4
print(student.add_grade(5))
# Output: Grade added: 5

print(f"Average: {student.average_grade()}")
# Output: Average: 4.666666666666667
```

The `add_grade` method modifies `self.grades` — the list stored in the object. Changes happen **in place**: the next call to `student.average_grade()` sees the updated state. It's not "return a new list", it's "modify the existing one".

## Dynamic attributes

In Python, you can add **any** attribute to an object at any time, even one that wasn't declared in `__init__`:

```python-executable
class Student:
    def __init__(self, name):
        self.name = name

student = Student("Maria")
student.age = 19              # added a new attribute on the fly
student.favorite_color = "blue"

print(student.age)
# Output: 19
print(student.favorite_color)
# Output: blue
```

Technically this works, but in real code you almost never do this. A few reasons:

-   **The object's state becomes unpredictable.** Looking at the `Student` class, you can't tell what attributes an object actually has.
-   **IDE and linter autocomplete won't help**: they only know what's declared in `__init__`.
-   **A typo creates a new attribute instead of raising an error.** If you write `student.aeg = 19` instead of `student.age = 19`, Python silently creates a new `aeg` field, and the bug is hard to spot.

The rule of thumb: declare all attributes in `__init__`, even with `None` if they'll be filled in later:

```python
class Student:
    def __init__(self, name):
        self.name = name
        self.age = None       # will be filled in later
        self.grades = []
```

That way the class honestly describes what fields an object has, and typos immediately become `AttributeError`.

## What's next?

In the next lesson we'll go deeper into attributes: the difference between instance and class attributes, and the classic mutable-default gotcha in `__init__`.

---

**What happens when you call `Person.greet(person)` if `greet` is defined with `self` as its first parameter?**

