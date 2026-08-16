---
meta:
    title: "Using Libraries in Python"
    description: "Introduction to Python libraries: what libraries are, how to import and use them, standard library and third-party modules."
---

# Using Libraries in Python

You need the value of π or the square root of a number. Deriving the formula and checking it yourself is slow and easy to get wrong, yet someone has already written and debugged that code for you. Ready-made sets of such code are called libraries: you plug the one you need in with a single line and use it.

## What are libraries?

> A library (or module) in Python is a file with code containing functions, classes, and variables that you can use in your programs.

## Importing libraries

To use a library, you first need to import it into your program. Python offers several ways to import:

### Importing the entire library

We plug in the whole module. Everything inside it stays behind the module's name, and we reach it through a dot:

```python
import math

radius = 5
circle_area = math.pi * radius ** 2
print(f"The area of a circle with radius {radius} is {circle_area:.2f}")
<output>
The area of a circle with radius 5 is 78.54
</output>
```

### Importing specific elements

We take only what we need out of the module. Those names land directly in your program, so there is no `math.` to type in front of them any more:

```python
from math import sqrt, floor

x = 16
result = sqrt(x)
print(f"The square root of {x} is {result}")
<output>
The square root of 16 is 4.0
</output>

y = floor(3.7)
print(f"Floor of 3.7 is {y}")
<output>
Floor of 3.7 is 3
</output>
```

### Importing with renaming

The same whole-module import, but under a short name. Handy when the full name is long or already taken by a variable of yours:

```python
import math as m

angle = 45
sin_value = m.sin(m.radians(angle))
print(f"The sine of {angle} degrees is {sin_value:.4f}")
<output>
The sine of 45 degrees is 0.7071
</output>
```

## Types of libraries in Python

In Python, there are three main types of libraries:

1. **Built-in modules** — modules that are already included in the Python standard library and are available immediately after installing Python.

2. **Third-party libraries** — modules created by other developers that need to be installed additionally.

3. **Custom modules** — modules that you create yourself to organize your code.

### Examples of built-in modules

```python
import random

random_number = random.randint(1, 10)
print(f"Random number: {random_number}")
<output>
Random number: 7
</output>

import datetime

current_date = datetime.datetime.now()
print(f"Current date and time: {current_date}")
<output>
Current date and time: 2023-07-15 14:30:45.123456
</output>
```

## Finding functions in the documentation

When working with libraries, it's important to know how to find information about available functions. Python provides several ways:

### Using the help() function

`help()` prints everything the module says about itself: what it is for, the list of functions, a description of each. For `math` that help is long, so what you see below is only its beginning, and the ellipsis at the end means it does not stop there.

```python
import math
help(math)
<output>
Help on module math:

NAME
    math

MODULE REFERENCE
    https://docs.python.org/3.12/library/math.html

    The following documentation is automatically generated from the Python
    source files.  It may be incomplete, incorrect or include features that
    are considered implementation detail and may vary between Python
    implementations.  When in doubt, consult the module reference at the
    location listed above.

DESCRIPTION
    This module provides access to the mathematical functions
    defined by the C standard.

FUNCTIONS
    acos(x, /)
        Return the arc cosine (measured in radians) of x.

        The result is between 0 and pi.
...
</output>
```

### Using the dir() function

```python
import random
attributes = dir(random)

# Let's display only the first 10 elements for brevity
print(attributes[:10])
<output>
['BPF', 'LOG4', 'NV_MAGICCONST', 'RECIP_BPF', 'Random', 'SG_MAGICCONST', 'SystemRandom', 'TWOPI', '_ONE', '_Sequence']
</output>
```

## Understanding Check

**Which of the following library import methods are correct in Python?**

1. **Correct answer:** import math — This is the correct way to import an entire library. After this, functions are used through the module name: math.sqrt(16).

2. **Correct answer:** from math import sqrt — This is the correct way to import a specific function from a module. After this, the function can be used directly: sqrt(16).

3. import sqrt from math — This is incorrect syntax. The correct way is: "from math import sqrt".

4. import math.sqrt — This syntax doesn't work in Python. You cannot import a specific function using dot notation.

In the next lesson we'll look at [built-in libraries](https://python-academy.org/en/guide/built-in-libraries) — the ones available right after you install Python.
