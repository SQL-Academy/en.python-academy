# pytest Basics: Writing Your First Tests in Python

In the previous article, we covered what testing is, why it's needed, and the different types of tests. Now it's time to get practical and meet one of the most popular testing frameworks in Python — `pytest`.

## Why pytest?

`pytest` has gained popularity due to several key advantages, especially important for beginners:

-   **Simple Syntax**: Tests use standard Python functions and plain `assert` statements. This makes test code cleaner and more understandable compared to more verbose xUnit frameworks.
-   **Automatic Test Discovery**: `pytest` automatically finds test files (default: `test_*.py` or `*_test.py`) and test functions (default: `test_*`) without needing explicit registration.
-   **Informative Output**: When tests fail, `pytest` provides detailed information that helps quickly pinpoint the cause of the problem.
-   **Powerful Ecosystem**: There are numerous plugins that extend `pytest`'s functionality (e.g., for working with web frameworks, generating coverage reports, etc.).
-   **Low Barrier to Entry**: It's easy to start writing simple tests and gradually learn more advanced features.

## Getting Started with pytest

### Installation

If you don't have `pytest` installed yet, you can do so using pip:

```bash
pip install pytest
```

### Your First Test

Create a file named `test_example.py`. `pytest` will automatically recognize it as a test file because of the `test_` prefix.

```python
# test_example.py

def add(x, y):
    return x + y

def test_add_positive_numbers():
    assert add(1, 2) == 3

def test_add_negative_numbers():
    assert add(-1, -2) == -3

def test_add_mixed_numbers():
    assert add(5, -2) == 3
```

In this example:

-   We defined a simple function `add()` that we want to test.
-   We wrote three test functions: `test_add_positive_numbers()`, `test_add_negative_numbers()`, and `test_add_mixed_numbers()`. Each one checks a specific aspect of the `add()` function's behavior.
-   Inside each test function, we use the `assert` keyword followed by an expression. If the expression is true, the test passes. If it's false, the test fails.

### Running Tests

Open your terminal, navigate to the directory where you saved `test_example.py`, and run the command:

```bash
pytest
```

`pytest` will find all files and functions matching its naming conventions and run them. You should see output similar to this:

```text
============================= test session starts ==============================
platform ... -- Python ...
plugins: ...
collected 3 items

test_example.py ...                                                      [100%]

============================== 3 passed in 0.XXs ===============================
```

A dot `.` for each test indicates that it passed successfully.

For more detailed output, use the `-v` (verbose) flag:

```bash
pytest -v
```

The output will include the names of the executed tests:

```text
============================= test session starts ==============================
platform ... -- Python ...
plugins: ...
collected 3 items

test_example.py::test_add_positive_numbers PASSED                        [ 33%]
test_example.py::test_add_negative_numbers PASSED                        [ 66%]
test_example.py::test_add_mixed_numbers PASSED                           [100%]

============================== 3 passed in 0.XXs ===============================
```

You can also tell `pytest` to run tests only from a specific file:

```bash
pytest test_example.py -v
```

## Understanding pytest Output: Success vs. Failure

It's important to be able to read `pytest` output, especially when tests fail.

### Successful Test

As we saw, successful tests are marked with a dot `.` (in normal mode) or the word `PASSED` (in `-v` mode).

### Failing Test

Let's intentionally break one of our tests in `test_example.py`:

```python
# test_example.py (with an error)

def add(x, y):
    return x + y

def test_add_positive_numbers():
    assert add(1, 2) == 3

def test_add_negative_numbers():
    assert add(-1, -2) == -3

def test_add_mixed_numbers_failed(): # Changed name for clarity and introduced an error
    assert add(5, -2) == 10 # Expected 3, but wrote 10
```

Now, let's run `pytest -v`:

```text
============================= test session starts ==============================
collected 3 items

test_example.py::test_add_positive_numbers PASSED                        [ 33%]
test_example.py::test_add_negative_numbers PASSED                        [ 66%]
test_example.py::test_add_mixed_numbers_failed FAILED                    [100%]

=================================== FAILURES ===================================
______________________ test_add_mixed_numbers_failed _______________________

    def test_add_mixed_numbers_failed(): # Changed name for clarity and introduced an error
>       assert add(5, -2) == 10 # Expected 3, but wrote 10
E       assert 3 == 10
E        +  where 3 = add(5, -2)

test_example.py:14: AssertionError
=========================== short test summary info ============================
FAILED test_example.py::test_add_mixed_numbers_failed - assert 3 == 10
========================= 1 failed, 2 passed in 0.XXs ==========================
```

What we see in the output for the failed test:

1.  **`FAILED`**: The status of the test.
2.  **`FAILURES` Section**: Detailed information about each failed test.
3.  **Traceback**: The path to the line in the code where the error occurred (`test_example.py:14: AssertionError`).
4.  **`E assert 3 == 10`**: `pytest` shows exactly what went wrong. It calculated `add(5, -2)` as `3` and compared it to `10`. Since `3 == 10` is false, the `assert` failed.
5.  **`short test summary info`**: A brief summary of the number of failed and passed tests.

This detailed output is very helpful for debugging.

## Organizing Tests

As your project grows, the number of tests will increase. `pytest` offers several ways to organize them.

### Test Files

As mentioned, `pytest` automatically discovers files whose names start with `test_` or end with `_test.py`.

### Test Functions

Inside these files, `pytest` looks for functions whose names start with `test_`.

### Grouping Tests in Classes

For better organization, you can group related tests into classes. The names of such classes should start with `Test`.

```python
# test_calculator.py

class Calculator:
    def add(self, x, y):
        return x + y

    def subtract(self, x, y):
        return x - y

# Group tests for the calculator in a class
class TestCalculator:
    def test_add(self):
        calc = Calculator()
        assert calc.add(2, 3) == 5
        assert calc.add(-1, 1) == 0

    def test_subtract(self):
        calc = Calculator()
        assert calc.subtract(5, 3) == 2
        assert calc.subtract(2, 5) == -3

# You can also have tests outside the class in the same file
def test_outside_class():
    assert True
```

Note that these classes do not need to inherit from any special base class (unlike `unittest.TestCase`, which we will discuss later). `pytest` will discover them and execute all `test_*` methods inside.

When you run `pytest -v`, you will see:

```text
test_calculator.py::TestCalculator::test_add PASSED
test_calculator.py::TestCalculator::test_subtract PASSED
test_calculator.py::test_outside_class PASSED
```

## What's Next?

We've covered the very basics of `pytest`: how to write simple tests, run them, and interpret the results. This is already a powerful tool for verifying the correctness of your code.

In the next article, we will delve into more advanced and useful features of `pytest`, such as fixtures (for managing test state) and parametrization (for running one test with different data).

---

**Which statement about the basics of working with `pytest` is correct?**
