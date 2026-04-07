# Testing in Python

Imagine building a bridge 🌉 - would you want people to cross it without first checking if it's safe? The same principle applies to your code! Testing helps ensure your programs work as expected, even when you make changes or add new features.

## What is testing?

> Testing in programming is the process of evaluating software to verify that it behaves as expected and to identify errors before users encounter them.

Testing helps:

-   Verify your code works correctly
-   Find bugs before your users do
-   Make changes with confidence
-   Document how your code should behave
-   Improve the design of your code

## Types of tests in Python

In Python, there are several levels of testing, each with its own purpose:

### Unit tests

```python
# Example of a simple function and its unit test
def add(a, b):
    return a + b

# Testing the function
result = add(2, 3)
expected = 5
print(f"Expected: {expected}, Got: {result}")
print(f"Test passed: {result == expected}")
```

Unit tests check individual components (functions, methods, classes) in isolation. They're like testing each brick before building a wall.

### Integration tests

Integration tests verify that different parts of your application work together correctly. Think of it as making sure all the gears in a machine mesh properly ⚙️.

### Functional tests

Functional tests ensure the application works as expected from a user's perspective. It's like having someone try to cross your bridge to make sure it's safe.

## Testing frameworks in Python

Python has several frameworks to make testing easier:

### Built-in: unittest

```python
import unittest

class TestAddFunction(unittest.TestCase):
    def test_add_positive_numbers(self):
        result = 2 + 3
        self.assertEqual(result, 5)

    def test_add_negative_numbers(self):
        result = -1 + (-2)
        self.assertEqual(result, -3)

# Normally you'd run with unittest.main(),
# but for this example we'll use a different approach:
test = TestAddFunction()
test.test_add_positive_numbers()
test.test_add_negative_numbers()
print("All tests passed!")
```

The `unittest` framework is included with Python and provides a rich set of tools for creating and running tests.

### pytest

```python
# test_example.py
def test_simple_calculation():
    assert 2 + 2 == 4

def test_string_operation():
    assert "hello" + " world" == "hello world"
```

`pytest` is a popular third-party framework that makes writing tests simpler and more readable.

### doctest

```python
def multiply(a, b):
    """
    Multiply two numbers and return the result.

    >>> multiply(2, 3)
    6
    >>> multiply(-1, 4)
    -4
    """
    return a * b

# In a real project, you'd use doctest.testmod()
print(multiply(2, 3))
print(multiply(-1, 4))
```

`doctest` lets you include tests in your documentation, which helps keep your examples accurate.

## Test-Driven Development (TDD)

Test-Driven Development is an approach where you write tests before writing the actual code. The process follows these steps:

1. Write a test for a feature that doesn't exist yet ✏️
2. Run the test and watch it fail ❌
3. Write the minimal code to make the test pass ✅
4. Refactor the code to improve its design while ensuring tests still pass 🔄
5. Repeat for the next feature

This approach helps you focus on what your code needs to do before thinking about how it will do it.

## Understanding Check

Let's check your understanding of testing basics:

**Which of these is NOT a benefit of testing?**


In the next lessons, we'll explore each testing approach in more detail, starting with why tests are essential for any serious software project.
