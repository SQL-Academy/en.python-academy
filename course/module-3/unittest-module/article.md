# unittest Module: Python's Built-in Testing Framework

In previous articles, we thoroughly explored `pytest`—a popular and powerful testing framework for Python—and discussed mocking concepts using `unittest.mock`. However, Python's standard library includes its own long-standing framework for writing tests: the `unittest` module.

Knowing `unittest` is important for several reasons:

-   **Understanding Fundamentals**: `unittest` follows the classic xUnit style, which formed the basis for many other testing frameworks.
-   **Working with Existing Code**: You may encounter projects where tests are already written using `unittest`.
-   **No External Dependencies**: Since `unittest` is built-in, it can be used without installing additional packages.

## Introduction to unittest

The `unittest` module, sometimes called "PyUnit," was inspired by JUnit (a Java framework).

Key features of `unittest`:

-   **Object-Oriented Approach**: Tests are organized into classes that inherit from `unittest.TestCase`.
-   **Specific Assertion Methods**: Instead of plain `assert`, methods like `self.assertEqual()`, `self.assertTrue()`, etc., are used.
-   **Setup and Teardown Structure**: Provides `setUp()` and `tearDown()` methods, along with class-level equivalents.

## unittest Basics

Let's look at a basic example.

```python
# test_simple_unittest.py
import unittest

def add(a, b):
    return a + b

class TestAddFunction(unittest.TestCase): # Class must inherit from unittest.TestCase
    def test_add_positive_numbers(self): # Test methods must start with test_
        result = add(3, 5)
        self.assertEqual(result, 8) # Use an assertion method

    def test_add_negative_numbers(self):
        result = add(-1, -1)
        self.assertEqual(result, -2)

    def test_add_mixed_numbers(self):
        result = add(-1, 1)
        self.assertEqual(result, 0)

if __name__ == '__main__':
    unittest.main() # Standard way to run tests from the file
```

If you save this code as `test_simple_unittest.py` and run it (`python test_simple_unittest.py`), you'll see output like this:

```text
...
----------------------------------------------------------------------
Ran 3 tests in 0.001s

OK
```

The structure of a `unittest` test:

1.  Import the `unittest` module.
2.  Create a test class that inherits from `unittest.TestCase`.
3.  Define test methods within the class whose names start with `test_`.
4.  Use specific `self.assert...()` methods within the test methods to check conditions.
5.  The `if __name__ == '__main__': unittest.main()` block allows running tests when the file is executed directly.

## Assertion Methods

The `TestCase` class provides numerous methods for various checks. Here are some of the most common ones:

| Method                                     | Checks that...                                                |
| ------------------------------------------ | ------------------------------------------------------------- |
| `assertEqual(a, b)`                        | `a == b`                                                      |
| `assertNotEqual(a, b)`                     | `a != b`                                                      |
| `assertTrue(x)`                            | `bool(x) is True`                                             |
| `assertFalse(x)`                           | `bool(x) is False`                                            |
| `assertIs(a, b)`                           | `a is b`                                                      |
| `assertIsNot(a, b)`                        | `a is not b`                                                  |
| `assertIsNone(x)`                          | `x is None`                                                   |
| `assertIsNotNone(x)`                       | `x is not None`                                               |
| `assertIn(member, container)`              | `member in container`                                         |
| `assertNotIn(member, container)`           | `member not in container`                                     |
| `assertIsInstance(obj, cls)`               | `isinstance(obj, cls)`                                        |
| `assertRaises(exception, callable, ...)`   | `callable` raises `exception`                                 |
| `assertRaisesRegex(exception, regex, ...)` | `callable` raises `exception` with a message matching `regex` |

Example using `assertRaises`:

```python
import unittest

def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b

class TestDivideFunction(unittest.TestCase):
    def test_divide_by_zero(self):
        with self.assertRaises(ValueError) as cm:
            divide(10, 0)
        self.assertEqual(str(cm.exception), "Cannot divide by zero")

    def test_divide_success(self):
        self.assertEqual(divide(10, 2), 5)

if __name__ == '__main__':
    unittest.main()
```

## Test Environment Setup and Teardown (Fixtures)

`unittest` provides methods for setting up the environment before tests and cleaning up afterward. This is analogous to fixtures in `pytest`.

-   `setUp()`: Executed before each test method in the class.
-   `tearDown()`: Executed after each test method, even if it raised an exception.
-   `setUpClass()`: A class method executed once before all tests in the class.
-   `tearDownClass()`: A class method executed once after all tests in the class.

```python
import unittest

class TestDatabaseOperations(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        # print("setUpClass: Connecting to test database...")
        cls.db_connection = "< simulated_db_connection >" # Simulation

    @classmethod
    def tearDownClass(cls):
        # print("tearDownClass: Closing database connection.")
        cls.db_connection = None

    def setUp(self):
        # print("\nsetUp: Creating test data...")
        self.user_data = {"id": 1, "name": "Test User"}

    def tearDown(self):
        # print("tearDown: Deleting test data...")
        self.user_data = None

    def test_user_creation(self):
        # print("Running test_user_creation")
        self.assertIn("name", self.user_data)
        self.assertTrue(self.db_connection)

    def test_user_property_access(self):
        # print("Running test_user_property_access")
        self.assertEqual(self.user_data["id"], 1)
        self.assertTrue(self.db_connection)

# Commented out as prints were removed for brevity
# if __name__ == '__main__':
#     unittest.main(verbosity=2)
```

## Running unittest Tests

There are several ways to run tests written with `unittest`:

1.  **From the file**: `python your_test_file.py` (if the file contains `unittest.main()`).
2.  **Via the `unittest` module from the command line**:
    -   `python -m unittest your_test_file.py`
    -   `python -m unittest test_module.TestClass`
    -   `python -m unittest test_module.TestClass.test_method`
    -   `python -m unittest discover`: Automatically finds and runs all tests (files matching `test*.py`) in the current directory and subdirectories.
        ```bash
        python -m unittest discover -s tests_directory -p "test_*.py"
        ```
        (`-s` specifies the directory to search, `-p` specifies the file pattern).

## Skipping Tests and Expected Failures

`unittest` provides decorators to control test execution:

-   `@unittest.skip(reason)`: Always skips the test. `reason` is a string explaining why.
-   `@unittest.skipIf(condition, reason)`: Skips the test if `condition` is true.
-   `@unittest.skipUnless(condition, reason)`: Skips the test if `condition` is false.
-   `@unittest.expectedFailure`: Marks the test as an "expected failure." If the test fails, it's considered a success (in a special category). If it unexpectedly passes, it's considered an error.

```python
import unittest
import sys

class TestSkipping(unittest.TestCase):
    @unittest.skip("Reason: demonstration of skip")
    def test_always_skipped(self):
        self.fail("This test should not have run")

    @unittest.skipIf(sys.version_info < (3, 10), "Requires Python 3.10+")
    def test_python_310_feature(self):
        self.assertTrue(True) # Logic for Python 3.10+

    @unittest.expectedFailure
    def test_broken_feature(self):
        self.assertEqual(1, 2, "This feature is not working correctly yet")

if __name__ == '__main__':
    unittest.main()
```

## unittest vs. pytest

Now that you are familiar with the basics of both `pytest` and `unittest`, let's compare them:

| Feature             | `unittest`                                          | `pytest`                                              |
| ------------------- | --------------------------------------------------- | ----------------------------------------------------- |
| **Style**           | OOP (`TestCase` classes), xUnit style               | Functional, less boilerplate                          |
| **Assertions**      | Specific methods (`self.assertEqual()`, etc.)       | Standard Python `assert` (more readable)              |
| **Fixtures**        | `setUp/tearDown` methods                            | Powerful fixtures with `@pytest.fixture`, DI, `scope` |
| **Parametrization** | Requires manual implementation or extensions        | Built-in (`@pytest.mark.parametrize`)                 |
| **Discovery**       | `test*.py` files, `Test*` classes, `test_*` methods | More flexible, fewer strict naming rules              |
| **Plugins**         | Limited ecosystem                                   | Extensive plugin ecosystem                            |
| **Community**       | Standard library, widely used                       | Very active community, de facto standard              |
| **Dependencies**    | Built-in, no external dependencies                  | Requires installation (`pip install pytest`)          |

**When might `unittest` be preferred?**

-   In legacy projects where it's already in use.
-   If there's a strict requirement against external dependencies.
-   For writing tests for the Python standard library itself.
-   If you prefer the classic xUnit style.

In most new projects, the Python community tends to favor `pytest` for its flexibility, simplicity, and powerful features.

## Using unittest.mock with unittest

As discussed in the article on mocks and stubs, the `unittest.mock` library is part of the standard library and works perfectly well with the `unittest` framework. The `patch` mechanism and `Mock` object creation are used in exactly the same way.

```python
import unittest
from unittest.mock import patch, Mock

# --- Code under test ---
class ExternalService:
    def get_data(self):
        # Real call we want to avoid in the test
        raise NotImplementedError("Don't call real service in test!")

class MyClass:
    def __init__(self):
        self.service = ExternalService()

    def process_data(self):
        data = self.service.get_data()
        # Some data processing
        return f"Processed: {data.get('key', 'no_key')}"
# --- End of code under test ---

class TestMyClassWithUnittest(unittest.TestCase):
    # Patch the get_data method on ExternalService
    @patch('__main__.ExternalService.get_data')
    def test_process_data_with_mock(self, mock_get_data):
        # Configure the mock
        mock_get_data.return_value = {"key": "mocked_value"}

        instance = MyClass()
        result = instance.process_data()

        self.assertEqual(result, "Processed: mocked_value")
        mock_get_data.assert_called_once() # Verify the mock was called

if __name__ == '__main__':
    unittest.main()
```

Here, `patch` is used to replace the `get_data` method of `ExternalService` with a mock during the execution of `test_process_data_with_mock`.

## What's Next?

We have covered the key aspects of the `unittest` framework. Understanding its principles is beneficial for any Python developer.

In the final article of this series, we will discuss how to measure the quality of our test coverage and how to automate the testing process using Continuous Integration (CI) systems.

---

**Which statement about the `unittest` module is correct?**
