# Modules in Python

Imagine you're writing a large program that gradually turns into hundreds or even thousands of lines of code. Keeping everything in one file becomes inconvenient — the code becomes difficult to maintain and it's hard to find the parts you need. This is where modules come to the rescue! 📦

## What is a module?

> A module in Python is simply a file with a `.py` extension,
> containing Python code (functions, classes, variables),
> which can be imported and used in other programs.

Modules help:

-   Organize related code into separate files
-   Reuse code in different programs
-   Avoid name conflicts
-   Make code more readable and maintainable

## Creating your own module

Creating a module in Python is very simple — just write code in a file with a `.py` extension:

Let's create a mymath.py file:

```python
# mymath.py

"""
Module with mathematical functions.
"""

# Constants
PI = 3.14159

# Functions
def add(a, b):
    """Addition of two numbers."""
    return a + b

def subtract(a, b):
    """Subtraction of one number from another."""
    return a - b

def multiply(a, b):
    """Multiplication of two numbers."""
    return a * b

def divide(a, b):
    """Division of one number by another."""
    if b == 0:
        raise ValueError("Division by zero is not possible")
    return a / b
```

That's it! Now we have a `mymath` module that contains a constant `PI` and four functions.

## Importing modules

To use your module in other programs, you need to import it. Python offers several import methods:

### Importing the entire module

```python
# Assume that the mymath.py file is in the same directory
import mymath

# Using functions from the module
result = mymath.add(5, 3)
print(f"5 + 3 = {result}")

# Using a constant from the module
area = mymath.PI * 5**2
print(f"Area of a circle with radius 5: {area}")
```

### Importing specific elements

```python
# Importing only specific functions
from mymath import add, multiply

# Now you can use functions directly, without the mymath prefix
print(f"5 + 3 = {add(5, 3)}")

print(f"5 * 3 = {multiply(5, 3)}")

# But other functions are not available
# print(subtract(5, 3))  # Will raise an error
```

### Importing with renaming

```python
# Importing a module with an alias
import mymath as mm

print(f"5 - 3 = {mm.subtract(5, 3)}")
```

### Importing all elements

```python
# Importing all elements from a module
# (this is generally not recommended, as it can lead to name conflicts)
from mymath import *

print(f"PI = {PI}")

print(f"5 / 2 = {divide(5, 2)}")
```

## Module structure

A good module should have a clear structure:

1. **Documentation** — at the beginning of the module (documentation strings in triple quotes)
2. **Imports** — first the standard library, then third-party libraries, then your own modules
3. **Constants** — global constants
4. **Classes** — class definitions
5. **Functions** — function definitions
6. **Execution code** — code that should be executed when the module is run as a standalone program

## Packages

When your project becomes large, you can organize related modules into packages.

> A package in Python is a directory containing modules and an `__init__.py` file, which indicates to Python that this directory should be treated as a package.

Example package structure:

```text
mypackage/
│
├── __init__.py
├── module1.py
├── module2.py
└── subpackage/
    ├── __init__.py
    └── module3.py
```

### Creating a package

1. Create a directory with the package name
2. Add an `__init__.py` file (can be empty)
3. Add your modules (`.py` files)

The `__init__.py` file is executed when the package is imported. It can be empty or contain initialization code:

```python
# mypackage/__init__.py

"""
My first package.
"""

# Initialization code
print("Package mypackage loaded")

# Variables available when importing the package
__version__ = '0.1'

# You can automatically import modules when importing the package
from . import module1
from . import module2
```

### Importing from a package

```python
# Assume we have a mypackage as described above

# Importing a module from a package
import mypackage.module1

# Importing specific elements from a module in a package
from mypackage.module2 import some_function

# Importing from a subpackage
import mypackage.subpackage.module3

# Relative imports (inside package modules)
# from . import module2  # import from the same directory
# from .. import module1  # import from the parent directory
```

## Special module variables

In Python, modules have several special variables:

### name

The `__name__` variable allows you to determine whether the module is run as a program or imported:

```python
# mymodule.py

def some_function():
    return "Function executed"

# This code will only run if the module is run directly
if __name__ == "__main__":
    print("Module run as a program")
    print(some_function())
else:
    print("Module imported")
```

If you run this module directly:

```bash
$ python mymodule.py
Module run as a program
Function executed
```

If you import this module:

```python
import mymodule  # Will output: "Module imported"
```

### all

The `__all__` variable defines which names will be imported when using `from module import *`:

```python
# mymodule.py

__all__ = ['public_function', 'PUBLIC_CONSTANT']

PUBLIC_CONSTANT = 42
_PRIVATE_CONSTANT = 43  # Starts with _, considered private

def public_function():
    return "This is a public function"

def _private_function():
    return "This is a private function"
```

Now:

```python
from mymodule import *
# Only available: public_function and PUBLIC_CONSTANT
# _PRIVATE_CONSTANT and _private_function are not imported
```

## Best practices when working with modules

1. **One module — one responsibility**: Each module should be responsible for one specific functionality.

2. **Clear module names**: Use descriptive but concise names (e.g., `data_processing.py`, `user_interface.py`).

3. **Documentation**: Each module should begin with documentation describing its purpose.

4. **Imports at the beginning of the file**: All imports should be at the beginning of the module.

5. **Explicit imports**: Prefer `from module import specific_thing` over `from module import *`.

6. **Private names**: Begin names of "private" functions and variables with an underscore (`_private_function`).

7. **Module testing**: Use the `if __name__ == "__main__":` block for testing the module.

## Understanding Check

Let's check how well you've understood the topic of modules:

**What will be output when executing the following code?**

```python
# file: test_module.py
def hello():
    return "Hello, world!"

if __name__ == "__main__":
    print("Module run directly")
else:
    print("Module imported")

# file: main.py
import test_module
print(test_module.hello())
```


Remember, modules and packages help apply the "Don't Repeat Yourself" (DRY) principle and make your programs more professional. Organize your code wisely, and it will be much easier for you to develop it in the future! 💪
