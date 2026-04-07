# Decorators in Python

Today we'll explore one of Python's most powerful and elegant features — decorators. They allow you to modify or enhance functions and methods without changing their source code! Think of decorators as special wrappers that add extra functionality to your code. 🎁

## What are Decorators?

> A decorator is a special kind of function that takes another function as input, extends or modifies its behavior, and returns the altered function without changing its source code.

Decorators follow a key principle in programming — the Open/Closed Principle, which states that code should be open for extension but closed for modification. With decorators, you can add new behaviors to functions without altering their original code.

## Basic Decorator Syntax

The basic syntax for using a decorator is very simple — you place the `@decorator_name` line right above the function definition:

```python
def my_decorator(func):
    def wrapper():
        print("Something happens before the function is called")
        func()  # Call the original function
        print("Something happens after the function is called")
    return wrapper

@my_decorator
def say_hello():
    print("Hello, world!")

# Call the decorated function
say_hello()
```

This is equivalent to:

```python
def my_decorator(func):
    def wrapper():
        print("Something happens before the function is called")
        func()
        print("Something happens after the function is called")
    return wrapper

def say_hello():
    print("Hello, world!")

# Decorate the function manually
decorated_hello = my_decorator(say_hello)

# Call the decorated function
decorated_hello()
```

## Decorators with Arguments

What if the function you're decorating takes arguments? You need to make your wrapper function accept and pass these arguments:

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Something happens before the function is called")
        result = func(*args, **kwargs)  # Call the original function with all arguments
        print("Something happens after the function is called")
        return result
    return wrapper

@my_decorator
def add(a, b):
    return a + b

# Call the decorated function with arguments
result = add(5, 3)
print(f"Result: {result}")
```

The `*args` and `**kwargs` syntax allows the wrapper to accept any number of positional and keyword arguments, making it flexible enough to work with any function.

## Practical Examples of Decorators

Let's explore a practical example of a decorator to see how it can be useful in real code.

### Timing Functions

A decorator to measure how long a function takes to execute:

```python
import time

def timing_decorator(func):
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = func(*args, **kwargs)
        end_time = time.time()
        execution_time = end_time - start_time
        print(f"Function {func.__name__} took {execution_time:.4f} seconds to execute")
        return result
    return wrapper

@timing_decorator
def calculate_sum(n):
    return sum(range(n))

calculate_sum(1000000)
```

## Decorators with Parameters

Sometimes you need decorators that can take their own parameters. This requires adding another level of nesting:

```python
def repeat(n=1):
    def decorator(func):
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
    return "Done"

say_hi("Alice")
```

In this example, we have three levels of functions:

1. `repeat(n)` — takes the decorator parameter
2. `decorator(func)` — takes the function to be decorated
3. `wrapper(*args, **kwargs)` — handles the function call

## Chaining Multiple Decorators

You can apply multiple decorators to a single function. They're executed from bottom to top (the decorator closest to the function is applied first):

```python
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
```

## Class Decorators

In addition to decorating functions, you can also create decorators for classes:

```python
def add_greeting(cls):
    # Add a new method to the class
    cls.greet = lambda self: f"Hello from {self.__class__.__name__}"
    return cls

@add_greeting
class Person:
    def __init__(self, name):
        self.name = name

# Create an instance and use the added method
person = Person("Alice")
print(person.greet())
```

## Real-World Use Cases for Decorators

Decorators are widely used in Python and many popular frameworks:

1. **Flask/Django routing** — map URLs to view functions

    ```python
    @app.route('/home')
    def home():
        return "Welcome to the home page!"
    ```

2. **Property decorators** — control access to attributes

    ```python
    class Person:
        def __init__(self, name):
            self._name = name

        @property
        def name(self):
            return self._name

        @name.setter
        def name(self, value):
            if not value:
                raise ValueError("Name cannot be empty")
            self._name = value
    ```

3. **Memoization/caching** — store function results to avoid repetitive calculations

    ```python
    def memoize(func):
        cache = {}
        def wrapper(*args):
            if args not in cache:
                cache[args] = func(*args)
            return cache[args]
        return wrapper
    ```

4. **Rate limiting** — limit how often a function can be called
5. **Validation** — validate inputs before processing
6. **Authorization** — check permissions before executing functions

## Understanding Check

**What is the primary purpose of decorators in Python?**


## Conclusion

Decorators are a powerful feature in Python that allow for elegant code organization and reuse. They help implement cross-cutting concerns like logging, authentication, and performance monitoring without cluttering the actual business logic of your functions.

As you continue your Python journey, you'll find decorators are an essential tool for writing clean, maintainable, and extensible code. They're especially prevalent in Python frameworks and libraries, so understanding how they work will make you more effective in using these tools.
