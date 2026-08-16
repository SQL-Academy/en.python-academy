---
meta:
    title: "Type Annotations in Python"
    description: "How to hint argument and result types: annotation syntax, collections, the X | None notation and type aliases."
---

# Type Annotations in Python

In large projects it can be hard to tell at a glance what data a function accepts. Compare two versions of the same stub:

```python
# What is user? An object? A string with a name? What does the function return?
def get_discount(user):
    pass

# Immediately clear: we pass an ID (integer), we get a discount (float)
def get_discount(user_id: int) -> float:
    pass
```

The `: int` and `-> float` marks are **type annotations**: hints about what types of values the function takes and returns.

> **Type annotations** are a special syntax for explicitly stating the expected data types of variables, function arguments and return values.

## Hints, not restrictions

Python remains a dynamically typed language: at runtime an annotation checks nothing. Let's make sure:

```python
def double(x: int) -> int:
    return x * 2

print(double(5))
<output>
10
</output>
print(double("ha"))   # the annotation didn't stop the string
<output>
haha
</output>
```

The program didn't crash: to Python an annotation is just a note. The value comes from the tools around your code — the editor and the IDE. We'll see exactly how at the end of the lesson.

## Basic syntax

For a variable, the type goes after the name, separated by a colon:

```python-interactive
name: str = "Alex"
age: int = 28
height: float = 1.82
is_developer: bool = True
```

In functions you annotate the arguments and — with the `->` arrow — the return value:

```python
def greet(name: str, age: int) -> str:
    return f"Hi, {name}! You are {age} years old."

message = greet("Ivan", 25)
print(message)
<output>
Hi, Ivan! You are 25 years old.
</output>
```

If a function returns nothing, write `-> None`.

## Annotating collections

For collections you also state the type of their contents:

```python-interactive
numbers: list[int] = [1, 2, 3, 4, 5]

# Keys are strings, values are numbers
user_ages: dict[str, int] = {
    "Ivan": 25,
    "Anna": 22
}

# A tuple with a fixed structure: (string, integer, float)
user_info: tuple[str, int, float] = ("Alex", 30, 75.5)
```

## A value or None

A common case: a function returns a value — or `None` when nothing was found. This is written with a vertical bar:

```python-interactive
def get_user_email(user_id: int) -> str | None:
    if user_id == 1:
        return "admin@example.com"
    return None
```

The same bar joins any types: `int | float` means "an integer or a float":

```python-interactive
def process_price(price: int | float) -> float:
    return float(price) * 1.2
```

> In older code you'll see `Optional[str]` instead of `str | None`, and `Union[int, float]` instead of `int | float`, from the `typing` module. They mean the same thing: the `|` notation appeared in Python 3.10 and is now preferred.

## Type aliases

To avoid repeating long types over and over, give them names:

```python-interactive
Coordinates = tuple[float, float]
UserDict = dict[str, str | int]

def get_location() -> Coordinates:
    return (55.7558, 37.6173)

def process_user(user: UserDict) -> None:
    pass
```

## Why bother

1. **Fewer bugs**: the editor underlines a mismatch before you even run the program, if you pass a string where an `int` is expected.
2. **Precise autocompletion**: the IDE knows the value's type — so it knows its methods.
3. **Easier reading**: the header `def get_user(user_id: int) -> dict[str, str]:` tells the whole story without reading the body.

## Understanding check

**How do you describe a function that takes an integer and a string-or-None, and returns a list of numbers?**

1. def process(id: int, name: str) -> list\[int]: — Here name is a required string, while the task says it may also be None.

2. def process(id: int, name: str | None) -> list: — The returned list has no content type — it should be list\[int].

3. **Correct answer:** def process(id: int, name: str | None) -> list\[int]: — str | None allows a string or None, and list\[int] specifies what the list contains.

4. def process(id: Integer, name: str | None) -> list\[int]: — Python's built-in type is called int, not Integer.
