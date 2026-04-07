# unittest Module: Standard Testing Library

In previous articles, we explored testing with pytest and became familiar with coverage and continuous integration concepts. However, Python also offers a built-in testing solution — the **unittest** module. Understanding it is valuable both for grasping the fundamentals of testing in Python and for working with codebases that use this module.

## Introduction to unittest

The `unittest` module is Python's standard library for testing, inspired by Java's JUnit testing framework. Unlike pytest, which we studied earlier, unittest uses an object-oriented approach to testing.

Key features of unittest:

-   **Built-in**: Included in Python's standard library, requires no additional installation
-   **OOP approach**: Tests are organized in classes that inherit from `unittest.TestCase`
-   **Rich assertion set**: Provides numerous methods for verifying results
-   **Setup and cleanup**: Supports methods for setting up and cleaning up the environment between tests

## unittest Basics

Let's create a simple test using unittest:

```python
import unittest

def add(a, b):
    return a + b

class TestAddFunction(unittest.TestCase):
    def test_add_positive_numbers(self):
        result = add(3, 5)
        self.assertEqual(result, 8)

    def test_add_negative_numbers(self):
        result = add(-1, -1)
        self.assertEqual(result, -2)

    def test_add_mixed_numbers(self):
        result = add(-1, 1)
        self.assertEqual(result, 0)

# Run the tests
if __name__ == '__main__':
    unittest.main()
```

As you can see, we:

1. Imported the `unittest` module
2. Created a test class inheriting from `unittest.TestCase`
3. Defined test methods starting with `test_`
4. Used the `assertEqual` method to verify results
5. Ran the tests through `unittest.main()`

## Assertion Methods

The `TestCase` class provides numerous assertion methods that help verify expected results:

| Method                                     | Purpose                                      |
| ------------------------------------------ | -------------------------------------------- |
| `assertEqual(a, b)`                        | Checks that `a == b`                         |
| `assertNotEqual(a, b)`                     | Checks that `a != b`                         |
| `assertTrue(x)`                            | Checks that `bool(x) is True`                |
| `assertFalse(x)`                           | Checks that `bool(x) is False`               |
| `assertIs(a, b)`                           | Checks that `a is b`                         |
| `assertIsNot(a, b)`                        | Checks that `a is not b`                     |
| `assertIsNone(x)`                          | Checks that `x is None`                      |
| `assertIsNotNone(x)`                       | Checks that `x is not None`                  |
| `assertIn(a, b)`                           | Checks that `a in b`                         |
| `assertNotIn(a, b)`                        | Checks that `a not in b`                     |
| `assertRaises(exc, func, *args, **kwargs)` | Checks that the function raises an exception |

### Example of Using Different Assertions

```python
import unittest

class TestAssertionMethods(unittest.TestCase):
    def test_equality(self):
        self.assertEqual(5, 5)
        self.assertNotEqual(5, 6)

    def test_boolean(self):
        self.assertTrue(True)
        self.assertFalse(False)
        self.assertTrue(1)  # 1 converts to True
        self.assertFalse(0)  # 0 converts to False

    def test_identity(self):
        a = [1, 2, 3]
        b = a  # b and a point to the same object
        c = [1, 2, 3]  # c is a different object with the same content
        self.assertIs(a, b)
        self.assertIsNot(a, c)

    def test_membership(self):
        self.assertIn(2, [1, 2, 3])
        self.assertNotIn(4, [1, 2, 3])

    def test_exceptions(self):
        with self.assertRaises(ZeroDivisionError):
            1 / 0

if __name__ == '__main__':
    unittest.main()
```

## Test Environment Setup and Cleanup

Unittest provides special methods for preparing the environment before tests run and cleaning up after them:

1. **setUp()**: Runs before each test
2. **tearDown()**: Runs after each test
3. **setUpClass()**: Runs once before all tests in the class
4. **tearDownClass()**: Runs once after all tests in the class

```python
import unittest

class DatabaseTest(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        print("Connecting to database (executed once)")
        cls.db_connection = "DB Connection"

    @classmethod
    def tearDownClass(cls):
        print("Closing database connection (executed once)")
        cls.db_connection = None

    def setUp(self):
        print("\nCreating test data (before each test)")
        self.test_data = [1, 2, 3]

    def tearDown(self):
        print("Cleaning up test data (after each test)")
        self.test_data = None

    def test_one(self):
        print("Running test 1")
        self.assertEqual(self.test_data, [1, 2, 3])

    def test_two(self):
        print("Running test 2")
        self.assertEqual(len(self.test_data), 3)

if __name__ == '__main__':
    unittest.main()
```

These methods are particularly useful when tests require common setup or cleanup of resources such as:

-   Database connections
-   Files and network connections
-   Creating and deleting temporary data

## Running Tests

Unittest offers several ways to run tests:

### In a Module

```python
if __name__ == '__main__':
    unittest.main()
```

### From the Command Line

```bash
python -m unittest test_module
python -m unittest tests/test_something.py
python -m unittest test_module.TestClass
python -m unittest test_module.TestClass.test_method
```

### With Test Discovery

```bash
python -m unittest discover -s tests
```

This command automatically finds all tests in the `tests` directory and runs them.

## Skipping Tests and Expected Failures

Unittest allows handling special cases in tests:

```python
import unittest
import sys

class TestSkippingAndFailing(unittest.TestCase):
    @unittest.skip("Demonstration of skipping")
    def test_skipped(self):
        self.fail("This test should not run")

    @unittest.skipIf(sys.version_info.minor < 10, "Requires Python 3.10+")
    def test_feature_requiring_python_310(self):
        # Test for functionality only available in Python 3.10+
        self.assertEqual(1, 1)

    @unittest.expectedFailure
    def test_expected_to_fail(self):
        # A test that should fail, but we know about it
        self.assertEqual(1, 0)

if __name__ == '__main__':
    unittest.main()
```

Decorators for special cases:

-   `@unittest.skip(reason)`: Always skips the test
-   `@unittest.skipIf(condition, reason)`: Skips the test if the condition is true
-   `@unittest.skipUnless(condition, reason)`: Skips the test if the condition is not true
-   `@unittest.expectedFailure`: Marks the test as expected to fail

## unittest vs pytest: When to Use Which?

| Feature          | unittest                           | pytest                                |
| ---------------- | ---------------------------------- | ------------------------------------- |
| Installation     | Standard library                   | Requires installation                 |
| Syntax           | OOP, more verbose                  | Functional, concise                   |
| Test methods     | In classes inheriting TestCase     | Regular functions named test\_\*      |
| Assertions       | Special methods (self.assertEqual) | Standard assert statements            |
| Fixtures         | setUp, tearDown                    | @pytest.fixture decorators            |
| Parameterization | Manual                             | Built-in via @pytest.mark.parametrize |
| Extensibility    | Good                               | Excellent (plugins, hooks)            |

**When to use unittest:**

-   In projects where an OOP approach is preferred
-   When you need to avoid external dependencies
-   When working with existing unittest code

**When to use pytest:**

-   For new projects
-   When code conciseness is important
-   When advanced features (fixtures, parameterization) are needed

## Working with Complex Scenarios

Unittest can handle more complex testing scenarios:

### Testing Classes

```python
import unittest

class Calculator:
    def __init__(self):
        self.result = 0

    def add(self, value):
        self.result += value
        return self.result

    def subtract(self, value):
        self.result -= value
        return self.result

    def reset(self):
        self.result = 0
        return self.result

class TestCalculator(unittest.TestCase):
    def setUp(self):
        self.calc = Calculator()

    def test_initial_state(self):
        self.assertEqual(self.calc.result, 0)

    def test_add_method(self):
        self.calc.add(5)
        self.assertEqual(self.calc.result, 5)
        self.calc.add(3)
        self.assertEqual(self.calc.result, 8)

    def test_subtract_method(self):
        self.calc.add(10)  # Add first so we have something to subtract
        self.calc.subtract(4)
        self.assertEqual(self.calc.result, 6)

    def test_reset_method(self):
        self.calc.add(10)
        self.calc.reset()
        self.assertEqual(self.calc.result, 0)

if __name__ == '__main__':
    unittest.main()
```

### Mocking with unittest.mock

The `unittest.mock` module provides tools for simulating objects during testing:

```python
import unittest
from unittest.mock import Mock, patch

# Assume we have a class that makes an HTTP request
class WebService:
    def fetch_data(self, url):
        # In a real scenario, this would make an HTTP request
        # But we don't want to make real requests in tests
        pass

class TestWebService(unittest.TestCase):
    def test_fetch_data_with_mock(self):
        service = WebService()

        # Create a mock for the fetch_data method
        service.fetch_data = Mock(return_value={"status": "success", "data": [1, 2, 3]})

        # Now calling fetch_data will return our mock data instead of making an HTTP request
        result = service.fetch_data("https://api.example.com/data")

        # Check that the method was called with the right argument
        service.fetch_data.assert_called_with("https://api.example.com/data")

        # Check the return value
        self.assertEqual(result["status"], "success")
        self.assertEqual(result["data"], [1, 2, 3])

    @patch('__main__.WebService.fetch_data')
    def test_fetch_data_with_patch(self, mock_fetch):
        # Configure the mock's behavior
        mock_fetch.return_value = {"status": "success", "data": [4, 5, 6]}

        # Create an instance where fetch_data is already mocked
        service = WebService()
        result = service.fetch_data("https://api.example.com/data")

        # Checks
        mock_fetch.assert_called_once()
        self.assertEqual(result["data"], [4, 5, 6])

if __name__ == '__main__':
    unittest.main()
```

## Best Practices with unittest

1. **Organize tests logically**: Group related tests in one class
2. **Isolate tests**: Each test should be independent and not rely on others
3. **Clear names**: Give tests descriptive names that reflect the behavior being tested
4. **One assertion per test**: When possible, make a single assertion per test for clarity of errors
5. **Use setUp/tearDown**: For common initialization and cleanup code
6. **Document tests**: Add docstrings explaining the purpose of tests

## Understanding Check

**Which statement about the unittest module is most accurate?**
