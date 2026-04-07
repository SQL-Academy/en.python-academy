# Type Annotations in Python

In large projects, it can be difficult to tell at a glance what data a function accepts or exactly what a variable holds. To make code more predictable and safe, Python introduced **type annotations**.

Compare two versions of the same function:

```python
# Before: what is user? An object? Database record? What does it return?
def get_discount(user):
    pass

# After: it is instantly clear we pass an ID (integer) and receive a discount (float)
def get_discount(user_id: int) -> float:
    pass
```

Annotations help developers and IDEs (Integrated Development Environments) clearly understand what data types are expected in different parts of the program, significantly speeding up coding and debugging.

## What are Type Annotations?

> **Type Annotations (Type Hints)** are a special syntax in Python that allows you to explicitly specify the expected data types for variables, function arguments, and return values.

It is crucial to understand: **Python remains a dynamically typed language**. Type annotations are merely _hints_. Python itself won't stop the execution of your program if you pass a string instead of a number at runtime. However, they are indispensable for:

-   Improving code autocompletion in your IDE (like PyCharm or VS Code)
-   Static code analysis (using tools like `mypy`)
-   Self-documenting code (making the code much easier to read and understand)

## Basic Syntax

### Variable Annotation

To annotate a variable, place a colon `:` after the variable name, followed by its type:

```python
# Annotating basic types
name: str = "Alex"
age: int = 28
height: float = 1.82
is_developer: bool = True
```

### Function Annotation

For functions, we can specify types for the passed arguments and the type of the return value (using the arrow `->`):

```python
def greet(name: str, age: int) -> str:
    return f"Hello, {name}! You are {age} years old."

message = greet("John", 25)
print(message)
```

In this example:

-   `name: str` — a string is expected
-   `age: int` — an integer is expected
-   `-> str` — the function promises to return a string

_(Note: if a function does not return anything, we use `None` as the return type, e.g., `-> None`)._

## Typing Collections (Lists, Dictionaries)

You can use standard collections to annotate their contents:

```python
# A list of integers
numbers: list[int] = [1, 2, 3, 4, 5]

# A dictionary where keys are strings and values are integers
user_ages: dict[str, int] = {
    "John": 25,
    "Anna": 22
}

# A tuple with a strict structure: (string, integer, float)
user_info: tuple[str, int, float] = ("Alex", 30, 75.5)

# A set of strings
unique_names: set[str] = {"John", "Anna", "Peter"}
```

## The typing Module

For more complex scenarios, we use the built-in `typing` module:

### Optional

If a variable can contain a value of a specific type **or** `None`, use `Optional`:

```python
from typing import Optional

def get_user_email(user_id: int) -> Optional[str]:
    if user_id == 1:
        return "admin@example.com"
    return None  # Return None if the user is not found
```

### Union

When a variable can take one of several possible types:

```python
from typing import Union

# The function can accept either an integer or a float
def process_price(price: Union[int, float]) -> float:
    return float(price) * 1.2  # Add 20% tax
```

### Callable (Functions as Arguments)

If you pass a function as an argument to another function, you can type it as well.

`Callable` takes two arguments: a list of input parameter types and the return type:

```python
from typing import Callable

def apply_twice(value: int, func: Callable[[int], int]) -> int:
    return func(func(value))

def double(x: int) -> int:
    return x * 2

result = apply_twice(3, double)  # double(double(3)) = 12
```

## Creating Custom Types (Type Aliases)

To avoid writing long, complex types multiple times, you can create **type aliases**:

```python
# Creating an alias
Coordinates = tuple[float, float]
UserDict = dict[str, str | int]

def get_location() -> Coordinates:
    return (55.7558, 37.6173)

def process_user(user: UserDict) -> None:
    pass
```

## Why Do We Need All This?

1. **Fewer Bugs**: Your code editor will highlight an error in red _even before running the program_ if you try to pass a string `""` where an `int` is expected.
2. **Perfect Autocompletion**: The IDE will know exactly what methods are available on the object because you specified its type.
3. **Easier to Read Code**: Reading the signature `def get_user(user_id: int) -> dict[str, str]:`, you instantly understand that the function takes a numeric ID and returns a dictionary. You don't have to read through the entire body of the function to figure that out.

## Checking Your Understanding

Let's check your knowledge of type annotations:

**How do you properly annotate a function that takes an integer and an optional string (which can be None), and returns a list of integers?**
