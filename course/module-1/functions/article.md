---
meta:
    title: "Functions in Python"
    description: "How to stop copying the same code around: declaring a function with def, parameters and arguments, return, and default values."
---

# Functions in Python

When the same block of code is needed in several places in a program, you don't have to copy it: you can describe it once as a function and then call it by name. A function takes input values, does something with them, and usually returns a result.

## What are functions?

> A function is a named block of code that performs a specific task and can be called from other parts of the program.

A useful way to think about a function is as a "black box": you feed in arguments, something happens inside, and a result comes back out.

![A function as a black box: arguments 5 and 3 enter the box def add(a, b): return a + b and 8 comes out](https://python-academy.org/static/guidePage/functions/function-box-en.webp "A function as a transformation input → output")

## Creating and calling functions

### Basic syntax

A function in Python is created using the keyword `def`:

```python
# Simple function example
def greet():
    print("Hello, world!")

# Function call
greet()
<output>
Hello, world!
</output>
```

Here `def` declares a function, `greet` is its name, the parentheses hold the parameters (none here), and after the colon comes the function body, indented.

### Functions with parameters

Functions can take data from outside. The names you write in the parentheses when declaring the function are called **parameters**, and the concrete values you pass in when calling it are **arguments**. The function below has one parameter — `name` — and we call it twice with different arguments: `"Anna"` and `"Peter"`.

```python
def greet(name):
    print(f"Hello, {name}!")

greet("Anna")
<output>
Hello, Anna!
</output>
greet("Peter")
<output>
Hello, Peter!
</output>
```

### Return values

Functions can return the result of their work using the `return` operator:

```python
# A function with a return value
def add(a, b):
    return a + b

# Using the function's result
result = add(5, 3)
print(f"5 + 3 = {result}")
<output>
5 + 3 = 8
</output>
```

`return` immediately exits the function: any code after it won't run. This is handy when you want to bail out early in special cases:

```python
def describe(value):
    if value < 0:
        return "negative"
    return "non-negative"

print(describe(-5))
<output>
negative
</output>
print(describe(10))
<output>
non-negative
</output>
```

If a function has no `return`, it still returns a value: `None`. That's fine for functions that don't compute anything but do something (like printing to the screen):

```python
def say_hi():
    print("Hi!")

result = say_hi()
print(result)
<output>
Hi!
None
</output>
```

## Function parameters

### Positional and keyword arguments

Python supports two ways of passing arguments to a function:

```python
# A function with multiple parameters
def describe_pet(animal_type, pet_name):
    print(f"I have a {animal_type} named {pet_name}.")

# Positional arguments
describe_pet("dog", "Rex")
<output>
I have a dog named Rex.
</output>

# Keyword arguments
describe_pet(pet_name="Whiskers", animal_type="cat")
<output>
I have a cat named Whiskers.
</output>
```

### Default parameter values

You can specify default values for parameters:

```python
# A function with a default parameter
def describe_pet(pet_name, animal_type="dog"):
    print(f"I have a {animal_type} named {pet_name}.")

# Using the default value
describe_pet("Rex")
<output>
I have a dog named Rex.
</output>

# Overriding the default
describe_pet("Whiskers", "cat")
<output>
I have a cat named Whiskers.
</output>
```

### Arbitrary number of arguments

Sometimes you don't know in advance how many arguments will be passed. Special parameters handle this: a star before the name (`*toppings`) gathers all positional arguments together, and two stars (`**user_info`) gather all keyword ones.

```python
# An arbitrary number of positional arguments
def make_pizza(*toppings):
    print("Toppings:", toppings)

make_pizza("pepperoni")
<output>
Toppings: ('pepperoni',)
</output>
make_pizza("mushrooms", "green peppers", "extra cheese")
<output>
Toppings: ('mushrooms', 'green peppers', 'extra cheese')
</output>

# An arbitrary number of keyword arguments
def build_profile(**user_info):
    print(user_info)

build_profile(name="Anna", location="Moscow", field="programming")
<output>
{'name': 'Anna', 'location': 'Moscow', 'field': 'programming'}
</output>
```

Python gathers all the passed values into a single structure: positional ones into a group of values, keyword ones into name-value pairs. What these structures are and how to go through them one by one, we'll cover in the chapters on collections and loops.

## Understanding check

Let's check how well you've absorbed the topic of functions:

**What will the following code output?**

```python
def mystery(x, y=2):
    return x * y

result = mystery(3)
print(result)
```

1. 3 — The function multiplies x by y, where y defaults to 2.

2. **Correct answer:** 6 — The function multiplies 3 by 2 (the default value for y).

3. Error — The code runs without errors, since y has a default value.

4. 9 — To get 9 you'd need to pass 3 and 3 as arguments.

## Practice tasks

Right below this article there's a block called **Practice tasks**. This is the first time you write a whole function: the `def` line with the name and parameters is already in the editor, and you fill in the body.

### Where the values come from

The values the function works with arrive in its parameters. You don't need to invent or enter them: each test calls the function with its own values.

![Anatomy of a test: its own parameter values, the function call and the expected result](https://python-academy.org/static/guidePage/functions/exercise-test-case-en.webp "Anatomy of a test case")

A test tab shows everything at once: which values it passes in, how it calls the function, and what result it expects. Every test uses its own values, and all of them need to pass.

### The result must be returned

The function must **return** its result with `return`. If you print it with `print()` instead, the test won't pass: the check looks at the returned value, not at what appeared on screen.
