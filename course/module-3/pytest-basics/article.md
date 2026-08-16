---
meta:
    title: "pytest basics: your first tests in Python"
    description: "Basic pytest usage: install, write tests with assert, run them and read the output. Minimum to get started."
---

# pytest basics: your first tests in Python

After every change you want to be sure nothing broke: you run an example, look at the output, check another one. By the fifth time it gets old — and that chore is exactly what `pytest`, the most popular Python testing framework, takes over. A test for it is a plain function with `assert`: you run `pytest` in the terminal, and it finds the tests itself and reports what passed and what failed.

## Install

```bash
pip install pytest
```

## Your first test

`assert` is a Python operator, not part of pytest. The statement `assert expression` does nothing if the expression is true, and raises `AssertionError` if it's false. That's what pytest stands on: a test is an ordinary function that either runs quietly to the end or fails on the first `assert` that doesn't hold.

Put production code in `example.py`, and put tests in a file next to it with a `test_` prefix. pytest finds tests by that prefix automatically:

**example.py**

```python
def add(x, y):
    return x + y

```

**test_example.py**

```python
from example import add

def test_add_positive():
    assert add(1, 2) == 3

def test_add_negative():
    assert add(-1, -2) == -3

def test_add_mixed():
    assert add(5, -2) == 3

```

A test is just a function whose name starts with `test_`, with regular Python `assert` inside. No special classes, no inheritance, no registration.

![Illustration: a project folder with files app.py, test_math.py, test_strings.py, helpers.py. pytest scans the folder and finds files with the test_ prefix, skipping the others](https://python-academy.org/static/guidePage/pytest-basics/pytest-discovery-en.webp "pytest auto-discovers test_*.py files and test_* functions")

## Running

In the terminal, in the directory with your tests:

```bash
pytest
```

pytest walks every `test_*.py` file, runs every `test_*` function inside, and prints the result:

```text
============================= test session starts ==============================
collected 3 items

test_example.py ...                                                      [100%]

============================== 3 passed in 0.02s ===============================
```

The dot `.` next to the filename means "one test passed". Three dots = three passed. For per-test output use `-v`:

```bash
pytest -v
```

```text
test_example.py::test_add_positive PASSED                                [ 33%]
test_example.py::test_add_negative PASSED                                [ 66%]
test_example.py::test_add_mixed PASSED                                   [100%]
```

## When a test fails

Let's break a test on purpose (`assert add(5, -2) == 10`, obviously wrong) and run `pytest`:

```text
test_example.py::test_add_mixed FAILED                                   [100%]

=================================== FAILURES ===================================
______________________________ test_add_mixed __________________________________

    def test_add_mixed():
>       assert add(5, -2) == 10
E       assert 3 == 10
E        +  where 3 = add(5, -2)

test_example.py:9: AssertionError
```

pytest shows:

- **where** it failed (`test_example.py:9: AssertionError`)
- **which expression** broke (`assert add(5, -2) == 10`)
- **what came out instead** (`3 == 10`, and that `3 = add(5, -2)`)

That detailed output usually tells you the cause immediately. This is the main argument for plain `assert` over special methods: pytest inspects the expression and shows the interesting parts.

## Checking that code fails

A test captures not only the right answer but the right error. The call `add("a", 1)` should fail with a `TypeError` — that's behavior too, and worth pinning down. A plain `assert` can't check it: the exception would abort the test before the check is reached. That's what `pytest.raises` is for:

```python
import pytest
from example import add

def test_add_string_and_number_raises():
    with pytest.raises(TypeError):
        add("a", 1)
```

The test passes if a `TypeError` is raised inside the `with` block, and fails if no exception occurs.

## Test structure: Arrange / Act / Assert

As tests get bigger, a pattern that hugely helps readability is **AAA (Arrange / Act / Assert)**:

1. **Arrange**: prepare data, create objects, set up state.
2. **Act**: do the thing you're actually testing.
3. **Assert**: verify the outcome.

```python
def test_user_can_change_email():
    # Arrange
    user = User("Anna", "old@example.com")

    # Act
    user.change_email("new@example.com")

    # Assert
    assert user.email == "new@example.com"
```

A simple test like `assert add(1, 2) == 3` fits on one line, and AAA isn't needed there. But once a test grows beyond 3-4 lines, splitting it into three blocks (with comments or just blank lines) makes it readable at a glance. It's the de facto standard in production code.

## Test names: what_when_expected

In real projects, the test name reads as a short statement about the code. A handy template: **`test_<what>_<under_what_conditions>_<expected_behaviour>`**:

- `test_add_returns_sum_for_positive_numbers` — clearer than `test_add_1`
- `test_withdraw_fails_when_balance_is_zero` — instantly tells you what and why
- `test_user_email_is_lowercased_after_save` — pinpoints the behaviour

When such a test fails in CI, the name alone tells you **what broke**, without reading the body. That saves hours of debugging in large projects.

## Understanding check

**Which statement about pytest basics is correct?**

1. You always have to pass test file names to pytest explicitly — pytest auto-discovers files starting with test\_ and functions starting with test\_. No need to list them by hand.

2. When a test fails, pytest just prints FAILED without details — It does the opposite: pytest shows the expression, the actual values and the line, so you understand the cause quickly.

3. **Correct answer:** pytest uses regular Python assert for checks — Correct. No special methods like self.assertEqual(), just plain assert, and pytest introspects it for nice output.

4. All tests in pytest must be class methods — Not required. Tests in pytest are top-level functions. Classes are optional for grouping.

The next article covers pytest's two superpowers: **fixtures** (shared test setup) and **parametrization** (one test, many inputs).
