---
meta:
    title: "Attributes and Special Methods"
    description: "How class attributes differ from instance attributes, how to avoid the mutable default value trap, and why you need __str__ and __eq__."
---

# Attributes and Special Methods

In the previous lesson all of an object's data lived in attributes like `self.name` — each object had its own. Today we'll look at data shared by all objects of a class, defuse a classic `__init__` trap, and teach objects to print and compare like proper values.

## Instance attributes and class attributes

The attributes that `__init__` creates via `self.name = ...` are called **instance attributes**: each object gets its own value. But some data is the same for everyone. Say all our students go to the same school — storing its name in every object makes no sense: the value is one for all, and if it changes, you'd have to update every object.

Such data is declared right in the class body, outside the methods. That's a **class attribute**:

```python
class Student:
    school = "School No. 1"      # class attribute — one for all

    def __init__(self, name):
        self.name = name         # instance attribute — each has its own

student1 = Student("Alex")
student2 = Student("Kate")

print(f"{student1.name}, {student1.school}")
<output>
Alex, School No. 1
</output>
print(f"{student2.name}, {student2.school}")
<output>
Kate, School No. 1
</output>
```

Notice: `student1.school` reads fine even though the object itself has no such attribute. When Python doesn't find the name in the instance, it looks it up in the class. So changing the class attribute is enough — all objects see the change at once:

```python
class Student:
    school = "School No. 1"

    def __init__(self, name):
        self.name = name

student1 = Student("Alex")
student2 = Student("Kate")

Student.school = "School No. 5"

print(f"{student1.name}, {student1.school}")
<output>
Alex, School No. 5
</output>
print(f"{student2.name}, {student2.school}")
<output>
Kate, School No. 5
</output>
```

`class Student`: `school = "School No. 1"`

- `student1`: `name = "Alex"`, `school` — from the class
- `student2`: `name = "Kate"`, `school` — from the class

`name` — each object has its own, while `school` lives in the class: one for all.

The rule of thumb is simple: whatever differs between objects goes into instance attributes (name, age), and whatever is one for all goes into class attributes (constants, shared settings, default values).

## The mutable default value trap

This `__init__` trap catches even experienced developers. Say we want a student to have an empty list of grades by default. It seems logical to write this:

```python
class Student:
    def __init__(self, name, grades=[]):   # looks harmless
        self.name = name
        self.grades = grades

s1 = Student("Anna")
s1.grades.append(5)
print(s1.grades)
<output>
[5]
</output>

s2 = Student("Ivan")
print(s2.grades)   # expecting []
<output>
[5]
</output>
```

Ivan ended up with Anna's grade, even though we added nothing to him. Why?

Default parameter values are evaluated **once**, at the moment the function is defined — not on every call. The list `[]` was created once and is reused by every `Student(...)` call without a `grades` argument. In other words, `s1.grades` and `s2.grades` point to **the very same** list in memory.

The correct pattern: use `None` as the default — the technique you know from the previous lesson — and create the real list inside:

```python
class Student:
    def __init__(self, name, grades=None):
        self.name = name
        if grades is None:
            grades = []
        self.grades = grades

s1 = Student("Anna")
s1.grades.append(5)
print(s1.grades)
<output>
[5]
</output>

s2 = Student("Ivan")
print(s2.grades)
<output>
[]
</output>
```

Now every object gets its own empty list. The same trick works for dictionaries, sets and any other mutable values.

## Special methods

The data is neatly stored in attributes — now let's see how the object behaves in everyday operations. Print it and compare two identical ones:

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

v1 = Vector(3, 4)
v3 = Vector(3, 4)

print(v1)
<output>
<__main__.Vector object at 0x7f9b1c2d3e50>
</output>
print(v1 == v3)
<output>
False
</output>
```

Printing produced the object's memory address instead of its contents, and the comparison answered `False` despite equal coordinates: without our help, Python compares objects by whether they are the same object in memory, not by content.

Both problems are solved with **special methods** — methods with double underscores in the name that Python calls itself at the right moment:

- `__init__` you already write in every class: it fires on `Student(...)`;
- `__str__` fires when the object needs to become a string — inside `print()`, for example;
- `__eq__` — on comparison with `==`;
- `__add__` — on addition with `+`.

Let's define the three new ones in `Vector`:

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"Vector({self.x}, {self.y})"

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

v1 = Vector(3, 4)
v2 = Vector(1, 2)
v3 = Vector(3, 4)

print(v1)
<output>
Vector(3, 4)
</output>
print(v1 + v2)
<output>
Vector(4, 6)
</output>
print(v1 == v3)
<output>
True
</output>
```

Note `__add__`: it doesn't change the original vectors — it builds a new object out of their coordinates and returns it.

Python has dozens of special methods: `__len__` teaches an object to answer `len(...)`, `__repr__` — to show itself while debugging. No need to memorize the list — they'll come up throughout the course as needed.

## Understanding check

**The program created two students. What does the last line print?**

```python
class Student:
    def __init__(self, name, grades=[]):
        self.name = name
        self.grades = grades

s1 = Student("Anna")
s1.grades.append(5)

s2 = Student("Ivan")
print(s2.grades)
```

1. \[] — The default list is not created on every call — it is created once: s2 got the very list that 5 was already appended to.

2. **Correct answer:** \[5] — The default value was evaluated once when \_\_init\_\_ was defined, so s1.grades and s2.grades are the same list.

3. An error: s2 has no grades attribute. — The attribute exists: \_\_init\_\_ stored the default list into self.grades.

4. \[5, 5] — The 5 was appended once, so the list holds a single value — even though the list is shared.

In the next lesson we'll look at inheritance: how one class continues another, reusing its attributes and methods.
