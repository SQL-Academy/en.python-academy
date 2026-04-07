# Using Libraries in Python

Libraries are sets of ready-made code that you can use in your programs. They allow you to avoid "reinventing the wheel" and achieve results faster by using solutions created by other developers.

## What are libraries?

> A library (or module) in Python is a file with code containing functions, classes, and variables that you can use in your programs.

Libraries help:

-   Save time by avoiding writing code "from scratch"
-   Use proven, optimized solutions
-   Extend the capabilities of the Python language
-   Create more complex programs with less effort

## Importing libraries

To use a library, you first need to import it into your program. Python offers several ways to import:

### Importing the entire library

```python
# Importing the entire math library
import math

# Using functions through the library name
radius = 5
circle_area = math.pi * radius ** 2
print(f"The area of a circle with radius {radius} is {circle_area:.2f}")
```

### Importing specific elements

```python
# Importing only specific functions from a module
from math import sqrt, pow

# Using imported functions directly
x = 16
result = sqrt(x)
print(f"The square root of {x} is {result}")

y = pow(2, 3)
print(f"2 to the power of 3 is {y}")
```

### Importing with renaming

```python
# Importing a module with an alternative name (alias)
import math as m

angle = 45
# Using m instead of math
sin_value = m.sin(m.radians(angle))
print(f"The sine of {angle} degrees is {sin_value:.4f}")
```

### Importing all elements

```python
# Importing all elements from a module
# Note: this method is generally not recommended
from math import *

radius = 3
# Using pi and cos directly, without specifying math
print(f"π is approximately {pi:.5f}")
print(f"The cosine of 0 is {cos(0)}")
```

## Types of libraries in Python

In Python, there are three main types of libraries:

1. **Built-in modules** — modules that are already included in the Python standard library and are available immediately after installing Python.

2. **Third-party libraries** — modules created by other developers that need to be installed additionally.

3. **Custom modules** — modules that you create yourself to organize your code.

### Examples of built-in modules

```python
# The random module for generating random numbers
import random

# Generating a random number from 1 to 10
random_number = random.randint(1, 10)
print(f"Random number: {random_number}")

# The datetime module for working with dates and times
import datetime

# Getting the current date and time
current_date = datetime.datetime.now()
print(f"Current date and time: {current_date}")
```

## Finding functions in the documentation

When working with libraries, it's important to know how to find information about available functions. Python provides several ways:

### Using the help() function

```python
# Getting information about a module
import math
help(math)  # Will only display the beginning of the help to avoid overloading the example
```

### Using the dir() function

```python
# Getting a list of all available attributes and methods
import random
attributes = dir(random)

# Let's display only the first 10 elements for brevity
print(attributes[:10])
```

## Understanding Check

Let's check how well you've understood the topic of libraries:

**Which of the following library import methods are correct in Python?**


In the following lessons, we will look at specific libraries and their applications in more detail.
