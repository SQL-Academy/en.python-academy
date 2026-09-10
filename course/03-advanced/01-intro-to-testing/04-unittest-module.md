---
meta:
    title: "The unittest module: the classic test framework"
    description: "unittest is the built-in xUnit framework in Python: TestCase, assertEqual/assertTrue methods, setUp/tearDown. What you meet in legacy code."
---

# The unittest module: the classic framework

You open an older codebase, and the tests don't look like the ones you just wrote: classes, inheritance from `TestCase`, checks via `self.assertEqual` instead of a plain `assert`. That's `unittest` — the testing framework from Python's standard library, in classic OOP style (familiar to anyone who's seen JUnit in Java). Most new projects pick `pytest`, but `unittest` hasn't gone anywhere and still shows up:

- In legacy code (the framework's been there since Python 2.1).
- In projects that ban external dependencies.
- In tests of the standard library itself.

Knowing the basics is useful: sooner or later you'll open someone else's repo with `unittest` and need to navigate it.

## Basic structure

```python
import unittest

def add(a, b):
    return a + b

class TestAddFunction(unittest.TestCase):
    def test_add_positive(self):
        self.assertEqual(add(3, 5), 8)

    def test_add_negative(self):
        self.assertEqual(add(-1, -1), -2)

if __name__ == "__main__":
    unittest.main()
```

What differs from pytest:

- Tests live in a **class** that inherits from `unittest.TestCase`.
- Method names start with `test_`, same as pytest.
- Instead of plain `assert`, you use **special methods**: `self.assertEqual(a, b)` instead of `assert a == b`.
- `unittest.main()` at the end is the entry point for `python test_file.py`.

Run via plain `python` or `python -m unittest`:

```bash
python test_addition.py
# or
python -m unittest test_addition.py
```

## The main assert methods

`TestCase` ships with many specialized assertions. Six come up daily:

| Method                      | Checks                     |
| --------------------------- | -------------------------- |
| `assertEqual(a, b)`         | `a == b`                   |
| `assertNotEqual(a, b)`      | `a != b`                   |
| `assertTrue(x)`             | `bool(x) is True`          |
| `assertFalse(x)`            | `bool(x) is False`         |
| `assertIn(item, container)` | `item in container`        |
| `assertRaises(Exception)`   | block raises the exception |

Example of `assertRaises` via a context manager:

```python
import unittest

def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b

class TestDivide(unittest.TestCase):
    def test_zero_division(self):
        with self.assertRaises(ValueError):
            divide(10, 0)

    def test_normal(self):
        self.assertEqual(divide(10, 2), 5)
```

## setUp and tearDown

`unittest` has no fixtures: it has `setUp()` (runs before each test) and `tearDown()` (runs after each test, even if the test failed):

```python
import unittest

class TestUserStorage(unittest.TestCase):
    def setUp(self):
        self.users = {"id": 1, "name": "Anna"}

    def tearDown(self):
        self.users = None

    def test_has_name(self):
        self.assertIn("name", self.users)

    def test_has_id(self):
        self.assertEqual(self.users["id"], 1)
```

![Illustration: class TestUserStorage(unittest.TestCase) containing a numbered sequence: 1.setUp 2.test_has_name 3.tearDown 4.setUp 5.test_has_id 6.tearDown; setUp/tearDown run again for each test](https://python-academy.org/static/guidePage/unittest-module/setup-teardown-en.webp "setUp and tearDown run for EVERY test")

For each test the cycle `setUp → test_X → tearDown` restarts; that guarantees tests are independent. There are also `setUpClass`/`tearDownClass` for one-time-per-class setup, but those are special cases.

## unittest vs pytest

| What            | unittest                      | pytest                     |
| --------------- | ----------------------------- | -------------------------- |
| Tests           | methods of a `TestCase` class | top-level functions        |
| Assertions      | `self.assertEqual()` etc.     | plain `assert`             |
| Setup           | `setUp` / `tearDown`          | fixtures with DI and scope |
| Parametrization | manual or via extensions      | `@pytest.mark.parametrize` |
| Dependencies    | built in                      | `pip install pytest`       |

pytest is more concise and more flexible, and is the default for new projects. But `unittest` is built in and needs no install; for scripts and stdlib tests that's a plus.

## Mocks in unittest

`unittest.mock` (which we covered in the previous article) is part of the same module. The same `@patch` and `Mock` work in `unittest` tests **exactly as in pytest**. The test method just receives `mock_*` as a parameter after `self`:

```python
import unittest
from unittest.mock import patch

class TestUserAPI(unittest.TestCase):
    @patch("requests.get")
    def test_get_user(self, mock_get):
        mock_get.return_value.json.return_value = {"id": 1}
        # ... testing code that uses requests.get
```

## Understanding check

**What is true about the unittest module?**

1. **Correct answer:** Test classes in unittest must inherit from unittest.TestCase — TestCase is what gives you the assert\* methods, setUp/tearDown, and the runner integration.

2. unittest uses plain assert for checks — No, unittest uses specialized methods (self.assertEqual, self.assertTrue etc.). Plain assert is the pytest style.

3. setUp runs once before all tests in the class — setUp runs before every single test. Once-per-class is setUpClass.

4. Test methods in unittest can be named anything — The runner only picks up methods whose name starts with test. A method with any other name simply will not run as a test.

Next (and last article in the testing module): how to measure **coverage** with `pytest-cov` and automate test runs with **CI** (using GitHub Actions as an example).
