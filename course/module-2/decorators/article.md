# Decorators in Python

Suppose we have several functions and we want to measure how long each one takes. We could copy the timing code into every function:

```python
def slow_function():
    start = time.time()
    # ... main work
    elapsed = time.time() - start
    print(f"slow_function: {elapsed:.4f} s")

def another_function():
    start = time.time()
    # ... main work
    elapsed = time.time() - start
    print(f"another_function: {elapsed:.4f} s")
```

It works, but next week we'll want to change the log format and we'll have to edit **every** function in turn. Plus the actual logic gets buried under boilerplate.

Decorators solve exactly this: you write the wrapper once and stick it on any function with a short `@wrapper` line above its definition.

## Basic syntax

A decorator is a function that takes another function and returns its "wrapped" version (with added behavior):

```python-executable
def my_decorator(func):
    def wrapper():
        print("Before the call")
        func()
        print("After the call")
    return wrapper

@my_decorator
def say_hello():
    print("Hello, world!")

say_hello()
# Output:
# Before the call
# Hello, world!
# After the call
```

Writing `@my_decorator` above `say_hello` is syntactic sugar for this equivalent:

```python
say_hello = my_decorator(say_hello)
```

That is, we reassign `say_hello` to the new function the decorator returned. No magic — just reassignment.

## Decorator with function arguments

If the wrapped function takes arguments, the wrapper has to forward them. The universal trick is `*args, **kwargs`:

```python-executable
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Before")
        result = func(*args, **kwargs)
        print("After")
        return result
    return wrapper

@my_decorator
def add(a, b):
    return a + b

print(add(5, 3))
# Output:
# Before
# After
# 8
```

`*args, **kwargs` means "accept any positional and keyword arguments", and `func(*args, **kwargs)` forwards them through. This trick makes the decorator universal — it works with any function.

## A practical decorator: timing

The very timing decorator we started with:

```python-executable
import time

def timing(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - start
        print(f"{func.__name__}: {elapsed:.4f} s")
        return result
    return wrapper

@timing
def calculate_sum(n):
    return sum(range(n))

calculate_sum(1_000_000)
# Output: calculate_sum: 0.0462 s
```

Now adding timing to any function is one line `@timing` on top. Want to change the log format? Edit the single `timing` function, not every function in the project.

## functools.wraps: preserving name and docstring

A naive decorator has a quiet side effect: the wrapped function "loses" its name and documentation, because from the outside you see the `wrapper`, not the original:

```python-executable
def timing(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@timing
def calculate_sum(n):
    """Sums numbers from 0 to n."""
    return sum(range(n))

print(calculate_sum.__name__)
# Output: wrapper
print(calculate_sum.__doc__)
# Output: None
```

In real code this breaks debugging, logging, and IDE introspection. It's fixed by one line — the `@functools.wraps(func)` decorator on `wrapper`:

```python-executable
from functools import wraps

def timing(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@timing
def calculate_sum(n):
    """Sums numbers from 0 to n."""
    return sum(range(n))

print(calculate_sum.__name__)
# Output: calculate_sum
print(calculate_sum.__doc__)
# Output: Sums numbers from 0 to n.
```

Rule of thumb: writing your own decorator? Always wrap the inner function with `@wraps(func)`. It's free and preserves introspection.

## Decorator with parameters

Sometimes you want to pass options to the decorator itself, e.g. "repeat the call N times". This needs another level: an outer function takes the parameter and returns the "real" decorator:

```python-executable
from functools import wraps

def repeat(n=1):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            result = None
            for _ in range(n):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(n=3)
def say_hi(name):
    print(f"Hi, {name}!")

say_hi("Anna")
# Output:
# Hi, Anna!
# Hi, Anna!
# Hi, Anna!
```

Three levels of nesting looks scary, but the logic is simple:

1. `repeat(n)` takes the decorator parameter and returns a regular decorator.
2. `decorator(func)` takes the function and returns a wrapper.
3. `wrapper(*args, **kwargs)` handles the actual call.

## Chaining decorators

You can apply more than one decorator. They apply **bottom-up**: the one closest to the function goes first:

```python-executable
def bold(func):
    def wrapper(*args, **kwargs):
        return f"<b>{func(*args, **kwargs)}</b>"
    return wrapper

def italic(func):
    def wrapper(*args, **kwargs):
        return f"<i>{func(*args, **kwargs)}</i>"
    return wrapper

@bold
@italic
def format_text(text):
    return text

print(format_text("Hello, world!"))
# Output: <b><i>Hello, world!</i></b>
```

`@italic` applies to `format_text` first — that gives an "italic function". Then `@bold` wraps it from outside — giving `bold(italic(format_text))`. So `<i>` closes first, then `<b>`.

## You've already met decorators

Back in encapsulation we used `@property` and `@balance.setter` — those are decorators from the standard library. `@property` takes a getter function and turns it into a "computed attribute". No magic: the same mechanism we're discussing now, applied in the context of classes.

## Where decorators live in the real world

A few places you'll meet them most often:

**Web frameworks (Flask, FastAPI, Django).** Wiring a URL to a handler function:

```python
@app.route('/home')
def home():
    return "Home page"
```

`@app.route` registers the function in the framework's router — without the decorator you'd have to call `app.add_url_rule(...)` for every endpoint manually.

**Caching.** Storing results so we don't recompute the same thing:

```python-executable
from functools import wraps

def memoize(func):
    cache = {}
    @wraps(func)
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrapper

@memoize
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)

print(fib(30))
# Output: 832040
```

Without `@memoize`, `fib(30)` would recompute the same calls millions of times and stall. With the cache — instantaneous. The standard library has this built in: `from functools import lru_cache`.

**Tests.** In pytest, `@pytest.fixture` and `@pytest.mark.parametrize` are decorators that turn a regular function into a fixture or a parametrized test.

## What's next?

Decorators are a workhorse of Python: libraries and frameworks are built on them (Flask routes, pytest fixtures, dataclasses, type checkers). Once you see that `@something` is just `func = something(func)`, the "magic" of these libraries disappears — it's all function composition underneath.

---

**What is the primary purpose of decorators in Python?**

