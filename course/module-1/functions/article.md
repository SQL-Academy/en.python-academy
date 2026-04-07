# Functions in Python

Imagine you're preparing a complex dish. Instead of coming up with a recipe from scratch each time, you can use an existing one. Functions in Python work in a similar way! 🧩

Functions are like ready-made recipes for your code. They allow you to create blocks of code that can be used multiple times, making your programs more organized and easier to understand.

## What are functions?

> A function is a named block of code that performs a specific task and can be called from other parts of the program.

Functions help to:

-   Organize code into logical blocks
-   Avoid code repetition
-   Make the program more readable
-   Simplify debugging and maintenance

## Creating and calling functions

### Basic syntax

A function in Python is created using the keyword `def`:

```python
# Simple function example
def greet():
    print("Hello, world!")

# Function call
greet()
```

In this example:

1. `def` indicates that we're creating a function
2. `greet` is the function name
3. `()` are brackets for parameters (empty for now)
4. `:` is the colon after the definition
5. Function body with indentation

### Functions with parameters

Functions can accept parameters — values that are passed to the function when it's called:

```python
# Function with parameters
def greet(name):
    print(f"Hello, {name}!")

# Function call with an argument
greet("Anna")
greet("Peter")
```

### Return values

Functions can return the result of their work using the `return` operator:

```python
# Function with a return value
def add(a, b):
    return a + b

# Using the function result
result = add(5, 3)
print(f"5 + 3 = {result}")
```

## Function parameters

### Positional and named parameters

Python supports two ways of passing arguments to a function:

```python
# Function with multiple parameters
def describe_pet(animal_type, pet_name):
    print(f"I have a {animal_type} named {pet_name}.")

# Positional arguments
describe_pet("dog", "Rex")

# Named arguments
describe_pet(pet_name="Fluffy", animal_type="cat")
```

### Default parameters

You can set default values for parameters:

```python
# Function with default parameters
def describe_pet(pet_name, animal_type="dog"):
    print(f"I have a {animal_type} named {pet_name}.")

# Using the default value
describe_pet("Rex")

# Overriding the default value
describe_pet("Fluffy", "cat")
```

### Arbitrary number of arguments

Sometimes you don't know in advance how many arguments will be passed to the function. For this, special parameters are used:

```python
# Function with an arbitrary number of positional arguments
def make_pizza(*toppings):
    print("Making a pizza with the following toppings:")
    for topping in toppings:
        print(f"- {topping}")

make_pizza("pepperoni")
make_pizza("mushrooms", "green peppers", "extra cheese")

# Function with an arbitrary number of named arguments
def build_profile(first, last, **user_info):
    profile = {}
    profile['first_name'] = first
    profile['last_name'] = last
    for key, value in user_info.items():
        profile[key] = value
    return profile

user_profile = build_profile('Anna', 'Smith',
                           location='New York',
                           field='programming')
print(user_profile)
```

## Understanding check

Let's check how well you've understood the topic of functions:

**What will the following code output?**

```python
def mystery(x, y=2):
    return x * y

result = mystery(3)
print(result)
```
