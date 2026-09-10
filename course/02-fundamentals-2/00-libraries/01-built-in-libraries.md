---
meta:
    title: "Python's built-in libraries"
    description: "An overview of the most useful built-in Python libraries, what they do, and practical usage examples."
---

# Python's built-in libraries

Compute a factorial, find today's date, shuffle a list, read JSON — none of this needs anything installed. It already ships with Python out of the box: dozens of ready-made modules available right after an `import`, with no `pip install`. This is the standard (built-in) library.

## What are built-in libraries?

> Built-in libraries (the standard library) are a set of modules included in the Python distribution that you can use without any additional installation.

## The main built-in libraries

Let's go through several of the most useful built-in libraries in Python.

### math: mathematical functions

The `math` module provides access to mathematical functions defined in the C language standard:

```python
import math

# Constants
print(f"Number π: {math.pi}")
<output>
Number π: 3.141592653589793
</output>
print(f"Number e: {math.e}")
<output>
Number e: 2.718281828459045
</output>

# Trigonometric functions
angle = math.pi / 4  # 45 degrees in radians
print(f"Sine of 45°: {math.sin(angle):.4f}")
<output>
Sine of 45°: 0.7071
</output>
print(f"Cosine of 45°: {math.cos(angle):.4f}")
<output>
Cosine of 45°: 0.7071
</output>

# Other functions
print(f"Factorial of 5: {math.factorial(5)}")
<output>
Factorial of 5: 120
</output>
print(f"Greatest common divisor of 12 and 18: {math.gcd(12, 18)}")
<output>
Greatest common divisor of 12 and 18: 6
</output>
```

### random: generating random numbers

The `random` module provides functions for generating random numbers and picking random elements:

```python
import random

# Generating a random integer in a range
print(f"Random number between 1 and 10: {random.randint(1, 10)}")
<output>
Random number between 1 and 10: 7
</output>

# Random float between 0 and 1
print(f"Random number between 0 and 1: {random.random():.4f}")
<output>
Random number between 0 and 1: 0.3528
</output>

# Picking a random element from a sequence
fruits = ["apple", "banana", "orange", "pear"]
print(f"Random fruit: {random.choice(fruits)}")
<output>
Random fruit: orange
</output>

# Shuffling a sequence
numbers = [1, 2, 3, 4, 5]
random.shuffle(numbers)
print(f"Shuffled numbers: {numbers}")
<output>
Shuffled numbers: [3, 1, 5, 2, 4]
</output>
```

### datetime: dates and times

The `datetime` module can parse dates from strings, add intervals to them, and format them back. That set is enough for typical date operations:

```python
from datetime import datetime, timedelta

# parse a string into a date using the "day.month.year" pattern
d = datetime.strptime("31.12.2022", "%d.%m.%Y")

# add an interval
new_d = d + timedelta(days=5)

# format the date back into a string
print(new_d.strftime("%d.%m.%Y"))
<output>
05.01.2023
</output>
```

This is just the tip of the iceberg — `datetime` can do a lot more, and it has its own chapter, "Working with dates and times", later in the module.

Two more big standard-library topics have chapters of their own, so here we'll just name the modules:

| What you need   | Module          | Where we cover it      |
| --------------- | --------------- | ---------------------- |
| Files and paths | `os`, `pathlib` | "Working with files"   |
| JSON and CSV    | `json`, `csv`   | "JSON and CSV formats" |

But `collections` didn't get its own chapter, so let's look at it right now.

### collections: specialised data types

The `collections` module provides several convenient data structures on top of the built-in ones. One of the most useful is `Counter` for counting elements:

```python
from collections import Counter

orders = ["apple", "banana", "apple", "cherry", "apple", "banana"]
counts = Counter(orders)

# most_common() sorts by count, highest first
print(counts.most_common())
<output>
[('apple', 3), ('banana', 2), ('cherry', 1)]
</output>
```

The module also has `defaultdict`, `namedtuple`, `deque` and others, advanced tools that will come in handy later.

## Understanding check

**Which library is best suited for working with dates in Python?**

1. math — The math library is meant for mathematical computation, not for working with dates.

2. **Correct answer:** datetime — The datetime library is purpose-built for working with dates and times.

3. random — The random library is used for generating random numbers, not for working with dates.

4. json — The json library is meant for working with the JSON format, not for dates.

In the next lesson we'll look at [third-party libraries](https://python-academy.org/en/guide/third-party-libraries), the ones installed via `pip install` that extend Python beyond the standard distribution.
