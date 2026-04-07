# Variable Scope in Python

Today we'll explore an important concept in programming — variable scope.

If variables are "boxes" for storing data, then scope determines where these boxes are available and can be used. It's like different rooms in a house — some things are available only in a specific room, while others are available throughout the entire house! 🏠

## What is scope?

> Variable scope is the part of a program where a variable is accessible for use. It defines where and how a variable can be used in the code.

Understanding variable scope is an **important skill** that helps you prevent errors and write more structured code. In Python, variables can have different scopes, which affects their accessibility and lifetime.

## Local and Global Variables

In Python, there are two main types of variables in terms of scope:

1. **Global variables** - created in the main body of the program and accessible in all parts of the program
2. **Local variables** - created inside functions and accessible only inside those functions

Imagine a school: global variables are like announcements on the main bulletin board that are visible to everyone, while local variables are like notes on boards in individual classrooms, visible only to students in that particular class.

![Description of variable scope differences](https://python-academy.org/static/guidePage/variable-scope/ru_scope.png 'Illustration of local and global variables')

### Global Variables

Global variables are defined outside of functions and can be used both inside functions and outside of them:

```python
# Create a global variable
message = "Hello, world!"

def show_message():
    # Use the global variable inside the function
    print(message)

show_message()  # Call the function

print(f"Variable outside the function: {message}")
```

Global variables are convenient when data needs to be accessible in all parts of the program, but their excessive use can make debugging and code maintenance more difficult.

### Local Variables

Local variables are created inside functions and exist only during the execution of that function. After the function completes, they are removed from memory:

```python
def calculate_sum():
    # Local variables
    a = 10
    b = 20
    result = a + b
    print(f"Sum inside the function: {result}")

calculate_sum()

# Attempt to access a local variable outside the function
try:
    print(f"Trying to access result: {result}")
except NameError as e:
    print(f"Error: {e}")
```

As you can see, the variable `result` exists only inside the `calculate_sum()` function and is not accessible outside of it. It's like leaving your belongings in a hotel room — they're only accessible while you're in the room! 🏨

### Priority of Local Variables

When a variable is created inside a function with the same name as a global variable, Python gives priority to the local version:

```python
x = "global"

def test_scope():
    x = "local"  # Local variable with the same name
    print(f"Inside function: x = {x}")

test_scope()

print(f"Outside function: x = {x}")
```

In this example, two different variables named `x` are created (local and global), despite having the same name. The local variable doesn't affect the global one and vice versa — they're like namesakes living in different cities! 👥

This is very important to understand in order to avoid confusion in your code!

## Modifying Global Variables

What if you need to modify a global variable inside a function? By default, Python interprets assigning a value to a variable inside a function as creating a new local variable:

```python
x = "global"

def modify_global():
    # Python will create a new local variable x
    x = "new local"  # This variable doesn't change the global one
    print(f"Inside function: x = {x}")

modify_global()

print(f"Outside function: x = {x}")
```

As we can see, the function didn't change the global variable. To directly modify a global variable inside a function, the `global` keyword is used.

### The global Keyword

The `global` keyword tells Python that the variable should be taken from the global scope, rather than created locally:

```python
x = "global"

def modify_global():
    global x  # Indicate that we're using the global variable
    x = "global modified"
    print(f"Inside function: x = {x}")

modify_global()

print(f"Outside function: x = {x}")
```

Now the function has successfully modified the global variable! It's like having a remote control that allows you to change things in another room! 📱

The `global` keyword also allows creating new global variables inside functions:

```python
def create_global_var():
    global new_var  # Create a global variable
    new_var = "This variable will be accessible outside the function"
    print("Variable created inside function")

create_global_var()

# Now the variable is accessible outside the function
print(f"Variable outside function: {new_var}")
```

But remember — with great power comes great responsibility! Use `global` only when it's truly necessary.

## Best Practices for Working with Scope

When working with variable scope in Python, it's recommended to follow these practices:

1. **Minimize the use of global variables** — they make code harder to understand and can lead to unpredictable results.

2. **Pass data through function parameters** — this makes the code more explicit and modular:

```python
def add(a, b):
    result = a + b
    return result

x = 5
y = 10
sum_result = add(x, y)  # Pass variables as arguments
print(f"{x} + {y} = {sum_result}")
```

3. **Use return values instead of modifying global variables**:

```python
def increment(value):
    return value + 1

count = 0
print(f"Initial value: {count}")

# Update the value through a return value
count = increment(count)
print(f"After increment: {count}")

# We can call the function multiple times
count = increment(increment(count))
print(f"After double increment: {count}")
```

## Understanding Check

<VariableScopeGuessOutput />

Now you understand how variable scope works in Python.
This is an important concept that will help you write more structured, understandable, and predictable code.
