---
meta:
    title: "Decorators in Python"
    description: "How decorators work: a wrapper around a function, *args and **kwargs, functools.wraps, and decorators with parameters."
---

# Decorators in Python

Suppose we have several functions and we want to measure how long each one takes. We could copy the timing code into every function:

```python
def slow_function():
    start = time.time()
    # ... main work
    elapsed = time.time() - start
    print(f"slow_function: {round(elapsed, 4)} s")

def another_function():
    start = time.time()
    # ... main work
    elapsed = time.time() - start
    print(f"another_function: {round(elapsed, 4)} s")
```

It works, but next week we'll want to change the log format and we'll have to edit **every** function in turn. Plus the actual logic gets buried under boilerplate.

Decorators solve exactly this: you write the wrapper once and stick it on any function with a short `@wrapper` line above its definition.

## A function is a value

One stepping stone first — decorators make no sense without it. A function in Python is a value like a number or a string: you can put it in a variable, pass it to another function and return it from a function.

```python
def say_hello():
    print("Hello!")

greet = say_hello   # no parentheses: we take the function itself, not its result
greet()
<output>
Hello!
</output>
```

`say_hello` without parentheses is the function itself; with parentheses it's a call. You already passed a function around in the previous lesson: `sorted(key=lambda ...)` is a function handed to another function. One thing remains: a function can be declared inside another one and returned — and that's exactly how a decorator works.

## Basic syntax

A decorator is a function that takes another function and returns its "wrapped" version (with added behavior):

```python
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
<output>
Before the call
Hello, world!
After the call
</output>
```

Writing `@my_decorator` above `say_hello` is a shorthand for this line:

```python
say_hello = my_decorator(say_hello)
```

That is, the name `say_hello` now points to the new function the decorator returned — an ordinary reassignment.

`say_hello()` → `@my_decorator` → `wrapper()`:

1. `print("Before the call")`
2. `say_hello()`
3. `print("After the call")`

The decorator returned a new function: the original inside, plus code before and after

## Decorator with function arguments

If the wrapped function takes arguments, the wrapper has to forward them. The universal trick is `*args, **kwargs`:

```python
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
<output>
Before
After
8
</output>
```

`*args, **kwargs` means "accept any positional and keyword arguments", and `func(*args, **kwargs)` forwards them through. This trick makes the decorator universal — it works with any function.

Notice the output order: `8` prints last, because the outer `print` waits for the wrapper to finish and return the result.

## A practical decorator: timing

The very timing decorator we started with. One detail: every function has a `__name__` attribute holding its name — the wrapper uses it to label the measurement:

```python
import time

def timing(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - start
        print(f"{func.__name__}: {round(elapsed, 4)} s")
        return result
    return wrapper

@timing
def calculate_sum(n):
    return sum(range(n))

calculate_sum(1_000_000)
<output>
calculate_sum: 0.0462 s
</output>
```

Now adding timing to any function is one line `@timing` on top. Want to change the log format? Edit the single `timing` function, not every function in the project.

## functools.wraps: preserving the function name

A naive decorator has a quiet side effect: the wrapped function "loses" its name, because from the outside you see the `wrapper`, not the original:

```python
def timing(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@timing
def calculate_sum(n):
    return sum(range(n))

print(calculate_sum.__name__)
<output>
wrapper
</output>
```

In real code this breaks debugging and error messages. It's fixed by one line — the `@functools.wraps(func)` decorator on `wrapper`:

```python
from functools import wraps

def timing(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@timing
def calculate_sum(n):
    return sum(range(n))

print(calculate_sum.__name__)
<output>
calculate_sum
</output>
```

Rule of thumb: writing your own decorator — always wrap the inner function with `@wraps(func)`.

## Decorator with parameters

Sometimes you want to pass options to the decorator itself, e.g. "repeat the call N times". This needs another level: an outer function takes the parameter and returns the "real" decorator:

```python
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
<output>
Hi, Anna!
Hi, Anna!
Hi, Anna!
</output>
```

Three levels of nesting looks scary, but the logic is simple:

1. `repeat(n)` takes the decorator parameter and returns a regular decorator.
2. `decorator(func)` takes the function and returns a wrapper.
3. `wrapper(*args, **kwargs)` handles the actual call.

Written out, it's the same trick as before, with one extra call:

```python
say_hi = repeat(n=3)(say_hi)
```

First the ordinary call `repeat(n=3)` runs — it returns the actual decorator, which is applied to `say_hi` right away.

The name `_` in the loop is a convention: the variable exists only to make the loop run, its value is never used.

## Where decorators live in the real world

A few places you'll meet them most often:

**Web frameworks.** Wiring a page address to a handler function — in Flask or FastAPI, for example:

```python
@app.route('/home')
def home():
    return "Home page"
```

`@app.route` registers the function in the framework's router: the browser requests `/home` — the framework calls `home()`.

**Caching.** The `@lru_cache` decorator from `functools` remembers a function's results: a repeated call with the same arguments recomputes nothing and returns the ready answer right away.

**Tests.** In pytest, the decorators `@pytest.fixture` and `@pytest.mark.parametrize` turn a regular function into a data setup or a whole series of tests — you'll meet them in the third module of the course.

## Understanding check

**What is the primary purpose of decorators in Python?**

1. **Correct answer:** To modify or extend functions without changing their source code — A decorator wraps a function to add behavior before, after, or around its call without touching the function itself.

2. To create new variables and data structures — Decorators work with functions, not with creating variables or data structures.

3. To optimize Python code for faster execution — Decorators can be used for optimization (e.g. caching via @lru_cache), but that is not their primary purpose.

4. To document code and generate API documentation — Decorators are sometimes used to attach metadata, but their main job is functional, not documentary.

Decorators are a workhorse of Python: web frameworks, tests and the `@property` you already know are all built on them. Once you know that `@something` is just `func = something(func)`, such code becomes much easier to read.

In the next lesson — working with dates and times: the `datetime` module, date arithmetic and formatting.
