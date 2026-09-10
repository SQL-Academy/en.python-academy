---
meta:
    title: "Variable scope in Python"
    description: "Local and global variables in Python, the global keyword, the typical UnboundLocalError trap, and how to properly pass data between functions."
---

# Variable scope in Python

Python programs are almost never written as one long wall of code. They're assembled from **functions** — separate pieces of code, each with its own name and its own job. You declare a function with the word `def`, and `return` says what result it hands back. We'll cover them properly in their own chapter; for now it's enough to be able to read this:

```python
def area_circle(radius):
    result = 3.14 * radius * radius
    return result

def area_square(side):
    result = side * side
    return result
```

Two functions here, and both use a variable named `result` inside. They don't interfere with each other: each function works with its own `result`, and one doesn't affect the other.

## What is variable scope?

The rule Python uses to decide which variable a name refers to at any given point in the code is called **variable scope**. It has two levels:

- **global** scope: anything declared in the main body of the program, visible inside every function
- **local** scope: variables declared inside a function, alive only while that function is running

A useful image: scopes are like rooms in an apartment. Things on your personal desk are accessible only to you (a local variable inside a function). A note on the fridge is visible to the whole family (a global variable).

![Global and local variables: main program and two functions](https://python-academy.org/static/guidePage/variable-scope/scope-en.webp "Local and global variables")

## Global variables

Global variables are defined outside any function and accessible both from within functions and outside them:

```python
# Create a global variable
message = "Hello, world!"

def show_message():
    # Use the global variable inside the function
    print(message)

show_message()
<output>
Hello, world!
</output>

print(f"Variable outside the function: {message}")
<output>
Variable outside the function: Hello, world!
</output>
```

The more globals you use, the harder the code is to debug: to understand what a function does, you have to look beyond its arguments and also remember which outside variables it reads.

## Local variables

Local variables are created inside a function and exist only while that function is running. Once it returns, they disappear from memory:

```python
def calculate_sum():
    a = 10
    b = 20
    result = a + b
    print(f"Sum inside the function: {result}")

calculate_sum()
<output>
Sum inside the function: 30
</output>

# Trying to access a local variable outside the function
try:
    print(f"result: {result}")
except NameError as e:
    print(f"Error: {e}")
<output>
Error: name 'result' is not defined
</output>
```

The variable `result` exists only inside `calculate_sum()` and doesn't leak out.

## When names collide

If a function creates a variable with the same name as a global one, the local one takes precedence. This is called **shadowing**: the local "shadows" the global without touching it:

```python
x = "global"

def test_scope():
    x = "local"
    print(f"Inside function: x = {x}")

test_scope()
<output>
Inside function: x = local
</output>

print(f"Outside function: x = {x}")
<output>
Outside function: x = global
</output>
```

These are two completely different variables that happen to share a name. Like two people with the same name living in different cities: same name, different individuals.

## Modifying a global variable from a function

By default, any assignment inside a function is treated as creating a new local variable:

```python
x = "global"

def modify_global():
    x = "new local"  # This is a local, the global is unchanged
    print(f"Inside function: x = {x}")

modify_global()
<output>
Inside function: x = new local
</output>

print(f"Outside function: x = {x}")
<output>
Outside function: x = global
</output>
```

To actually have a function change a global variable, you must explicitly say `global x` at the start of the function:

```python
x = "global"

def modify_global():
    global x
    x = "global changed"
    print(f"Inside function: x = {x}")

modify_global()
<output>
Inside function: x = global changed
</output>

print(f"Outside function: x = {x}")
<output>
Outside function: x = global changed
</output>
```

The `global` keyword signals to anyone reading the code: "this function isn't just doing its own job, it reaches outside." For that reason, use it only when no other path is available.

## The most common trap: UnboundLocalError

A function reads a global variable and immediately reassigns it:

```python
x = 10

def show():
    print(x)        # read
    x = x + 1       # and reassign right after
```

You'd expect the function to print `10` and bump `x` up to `11`. Instead, running it crashes with `UnboundLocalError: local variable 'x' referenced before assignment`.

What's going on? Python looks at the function **as a whole** before executing it and decides which names are local. It sees `x = ...` inside, so `x` is declared in this function and is therefore local. On the top line, `print(x)` then tries to read the **local** `x`, which doesn't yet have a value, and crashes.

For this function to work with the global `x`, you'd need `global x` at the top. But usually there's a cleaner path: don't touch the global, take `x` as a parameter and return the new value:

```python
def increment(value):
    return value + 1

x = 10
x = increment(x)
print(x)
<output>
11
</output>
```

## When global is justified, and when it isn't

The most predictable way to handle data is: don't modify globals; pass values through parameters and return new ones via `return`:

```python
def add(a, b):
    return a + b

x = 5
y = 10
sum_result = add(x, y)
print(f"{x} + {y} = {sum_result}")
<output>
5 + 10 = 15
</output>
```

This kind of function is honest: its result is fully determined by what came in. Nothing happens behind the scenes. That makes both testing and reading easier: a glance at the signature tells you everything.

`global` is justified when a variable really should live at program level: a configuration value, a shared counter. But in most practice tasks it isn't needed.

## Understanding check

Let's see how well you've understood variable scope.

The interactive demonstration is available [in the Python Academy lesson](https://python-academy.org/en/guide/variable-scope).

In the next lesson we'll look at basic data types in Python: what's behind `int`, `str`, `float`, `bool`, and which operations are defined on each.
