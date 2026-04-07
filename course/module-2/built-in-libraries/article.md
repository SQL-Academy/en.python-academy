# Python Built-in Libraries

Imagine you just got a new smartphone. Right after purchase, it already has a calendar, calculator, camera, and other useful applications installed. Python's built-in libraries work in a similar way! 🧰

Built-in libraries are modules that come with Python and are available immediately after installation. They provide ready-made solutions for the most common programming tasks.

## What are built-in libraries?

> Built-in libraries (standard library) are a set of modules that are included in the Python distribution and can be used without additional installation.

The Python standard library includes:

-   Modules for system interaction
-   Tools for data processing
-   Utilities for network operations
-   Tools for creating user interfaces
-   And much more

## Main built-in libraries

Let's look at several of the most useful Python built-in libraries.

### math — mathematical functions

The `math` module provides access to mathematical functions defined in the C language standard:

```python
import math

# Constants
print(f"Number π: {math.pi}")
print(f"Number e: {math.e}")

# Trigonometric functions
angle = math.pi / 4  # 45 degrees in radians
print(f"Sine of 45°: {math.sin(angle):.4f}")
print(f"Cosine of 45°: {math.cos(angle):.4f}")

# Other functions
print(f"Factorial of 5: {math.factorial(5)}")
print(f"Greatest common divisor of 12 and 18: {math.gcd(12, 18)}")
```

### random — generating random numbers

The `random` module provides functions for generating random numbers and selecting random elements:

```python
import random

# Generating a random integer in a range
print(f"Random number from 1 to 10: {random.randint(1, 10)}")

# Random floating-point number from 0 to 1
print(f"Random number from 0 to 1: {random.random():.4f}")

# Selecting a random element from a sequence
fruits = ["apple", "banana", "orange", "pear"]
print(f"Random fruit: {random.choice(fruits)}")

# Shuffling a sequence
numbers = [1, 2, 3, 4, 5]
random.shuffle(numbers)
print(f"Shuffled numbers: {numbers}")
```

### datetime — working with dates and time

The `datetime` module provides classes for working with dates and time:

```python
import datetime

# Current date and time
now = datetime.datetime.now()
print(f"Current date and time: {now}")

# Creating a specific date
specific_date = datetime.date(2023, 12, 31)
print(f"Specified date: {specific_date}")

# Difference between dates
today = datetime.date.today()
new_year = datetime.date(today.year + 1, 1, 1)
days_until_new_year = (new_year - today).days
print(f"Days until New Year: {days_until_new_year}")

# Formatting dates
formatted_date = now.strftime("%d.%m.%Y %H:%M")
print(f"Formatted date: {formatted_date}")
```

### os — interacting with the operating system

The `os` module provides functions for interacting with the operating system:

```python
import os

# Getting the current working directory
print(f"Current directory: {os.getcwd()}")

# List of files and folders in a directory
files = os.listdir('.')
print(f"First 3 files in the current directory: {files[:3]}")

# System information
print(f"Operating system name: {os.name}")

# Check if a file or directory exists
file_exists = os.path.exists('example.txt')
print(f"File example.txt exists: {file_exists}")
```

### json — working with JSON format

The `json` module provides functions for working with data in JSON format:

```python
import json

# Python dictionary
person = {
    "name": "John",
    "age": 30,
    "city": "New York",
    "languages": ["Python", "JavaScript", "SQL"]
}

# Converting a dictionary to a JSON string
person_json = json.dumps(person, indent=4)
print("JSON string:")
print(person_json)

# Converting a JSON string to a Python object
json_string = '{"name": "Mary", "age": 25, "city": "San Francisco"}'
person_dict = json.loads(json_string)
print(f"Name: {person_dict['name']}, Age: {person_dict['age']}")
```

### collections — specialized data types

The `collections` module provides alternative data structures for Python:

```python
from collections import Counter, defaultdict, namedtuple

# Counter - counting elements
text = "Programming in Python is interesting and enjoyable"
character_count = Counter(text.lower())
print("Three most frequent characters:")
for char, count in character_count.most_common(3):
    print(f"'{char}': {count}")

# defaultdict - dictionary with default value
fruit_categories = defaultdict(list)
fruit_categories["yellow"].append("banana")
fruit_categories["red"].append("apple")
fruit_categories["red"].append("strawberry")
print(f"Yellow fruits: {fruit_categories['yellow']}")
print(f"Green fruits: {fruit_categories['green']}")  # Empty list

# namedtuple - named tuples
Person = namedtuple('Person', ['name', 'age', 'job'])
alice = Person('Alice', 30, 'engineer')
print(f"{alice.name}, {alice.age} years old, works as an {alice.job}")
```

## Understanding Check

Let's check how well you've understood the topic of built-in libraries:

**Which library is best suited for working with dates in Python?**
