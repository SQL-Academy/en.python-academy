# Variables in Python

Hello! Today we'll get acquainted with one of the most important concepts in programming — variables. Think of them as labeled "boxes" (named memory areas) where you can store different things: numbers, texts, lists, and much more.

## What is a variable?

> A variable in Python is a named reference to an object in memory, whose type is determined dynamically and can change.

Working with variables in Python is extremely simple:

-   No need to declare variables before using them
-   No need to specify the type — Python will determine it automatically
-   You can change the variable type at any time

A variable is created at the first assignment, and then you can use it anywhere in your program.

## Creating and using variables

To create a variable, use the `=` operator.  
Example:

```python
# Creating variables of different types
name = "John"          # String variable
age = 25               # Integer variable
height = 1.85          # Float variable
is_student = True      # Boolean variable
courses = ["Python", "SQL", "JavaScript"]  # List variable
```

After this, when executing `print()` or other operations, the variable name will be replaced with its value:

```python
name = "John"
# Using the variable in a greeting string
print("Hello, " + name + "! 👋")

age = 25
# Using the variable in a calculation
next_year_age = age + 1
print(f"Next year you will be {next_year_age} years old! 🎂")
```

## Multiple assignment

Python allows you to assign values to multiple variables in a single line:

```python
# Assigning the same value to multiple variables
x = y = z = 0
print(f"x = {x}, y = {y}, z = {z}")

# Assigning different values to multiple variables
a, b, c = 1, 2, 3
print(f"a = {a}, b = {b}, c = {c}")
```

You can also "unpack" a collection into multiple variables:

```python
coordinates = (10, 20, 30)
x, y, z = coordinates
print(f"Coordinates: x={x}, y={y}, z={z}")
```

To swap values between two variables without using an intermediate variable, use simple assignment:

```python
# Swapping variable values
a = 5
b = 10
print(f"Before swap: a = {a}, b = {b}")

a, b = b, a  # Value swap
print(f"After swap: a = {a}, b = {b}")
```

## Dynamic typing

Python is a dynamically typed language. The variable type is determined during execution and can change when a new value is assigned:

```python
# Demonstrating variable type changes
x = 10 # x has type int (integer)
print(f"x = {x}, type: {type(x)}")

x = "ten" # now x has type str (string)
print(f"x = {x}, type: {type(x)}")

x = [1, 2, 3] # now x has type list (list)
print(f"x = {x}, type: {type(x)}")
```

To determine the current data type of a variable, use the built-in `type()` function:

```python
name = "John"
age = 25
height = 1.85
is_student = True

# Determining variable types
print(f"{name}: {type(name)}")
print(f"{age}: {type(age)}")
print(f"{height}: {type(height)}")
print(f"{is_student}: {type(is_student)}")
```

## Naming variables in Python

Good variable names make code understandable and maintainable.

### Naming rules

1. Names can contain letters, numbers, and underscores (`a-z`, `A-Z`, `0-9`, `_`).
2. Names must start with a letter or underscore.
3. You cannot use Python reserved words (e.g., `if`, `for`, `class`).

```python
# Valid names
name = "John"
age_in_years = 25
_private_variable = "Non-public information"

# Invalid names
# 2name = "Cannot start with a digit"
# my-name = "Cannot use hyphens"
# class = "Reserved word"
```

### Best practices for naming variables

In the Python community, there is an official style guide — [PEP 8](https://peps.python.org/pep-0008/), which contains recommendations for naming variables and other aspects of writing clean, understandable Python code.

Following these recommendations is considered good practice and helps write code that is easy to maintain:

1. **Use descriptive names** — let the variable name indicate its purpose

```python
# Better this way ✅
user_age = 25
print(f"User age: {user_age}")

# Than this ❌
a = 25
print(f"Variable a value: {a}")  # What is this variable about?
```

2. **Use snake_case style** for variables (lowercase words separated by underscores)

```python
# Python style ✅
first_name = "John"
print(f"Name: {first_name}")

# Not Python style ❌
firstName = "John"
print(f"Name: {firstName}")
```

3. **Add the prefix `is_` or `has_`** for boolean variables (bool type)

```python
# It's immediately clear these are boolean variables ✅
is_adult = True
has_permission = False

print(f"Is adult: {is_adult}")
print(f"Has permission: {has_permission}")
```

4. **Use UPPER_CASE** for constants (values that shouldn't change)

    ```python
    # Constants in uppercase ✅
    MAX_ATTEMPTS = 3
    PI = 3.14159
    ```

5. **Avoid overly short and unclear names** — saving on characters leads to time wasted when reading code

    ```python
    # Poor variable names ❌
    n = "John"          # Too short
    x = 25              # Unclear
    flag = True         # Vague purpose
    str1 = "String"     # Uninformative

    # Good variable names ✅
    user_name = "John"
    age_in_years = 25
    is_verified = True
    welcome_message = "Welcome!"
    ```

### Case sensitivity

Python is a case-sensitive language, which means there's a distinction between uppercase and lowercase letters in variable names. The variables `name`, `Name`, and `NAME` are interpreted as three different variables:

```python
name = "John"
Name = "Peter"
NAME = "Alex"

print(name)
print(Name)
print(NAME)
```

These are three different variables: changing the case in a variable name creates a completely new variable.

## Understanding check

Let's check how well you've understood the features of working with variables in Python:

**Which of the following statements most accurately describes variables in Python?**


## Conclusion

Great! Now you know what variables are in Python and how to work with them. This is a fundamental concept that will be the foundation for all your future journey in the programming world.

Remember that proper variable naming and understanding their dynamic nature in Python will make your code more readable and maintainable. Experiment, practice, and you'll definitely succeed!
