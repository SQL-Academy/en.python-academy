# pytest basics: your first tests in Python

`pytest` is the most popular Python testing framework. Its entry point is simple: you write regular Python functions with `assert`, then run `pytest` from the terminal. It auto-discovers your tests and reports what passed and what failed.

## Install

```bash
pip install pytest
```

## Your first test

Put production code in `example.py`, and put tests in a file next to it with a `test_` prefix. pytest finds tests by that prefix automatically:

<CodeProject
    defaultFile="test_example.py"
    files={[
        {
            path: 'example.py',
            content: `def add(x, y):
    return x + y
`,
        },
        {
            path: 'test_example.py',
            content: `from example import add

def test_add_positive():
    assert add(1, 2) == 3

def test_add_negative():
    assert add(-1, -2) == -3

def test_add_mixed():
    assert add(5, -2) == 3
`,
        },
    ]}
/>

A test is just a function whose name starts with `test_`, with regular Python `assert` inside. No special classes, no inheritance, no registration.

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

-   **where** it failed (`test_example.py:9: AssertionError`)
-   **which expression** broke (`assert add(5, -2) == 10`)
-   **what came out instead** (`3 == 10`, and that `3 = add(5, -2)`)

That detailed output usually tells you the cause immediately. This is the main argument for plain `assert` over special methods: pytest inspects the expression and shows the interesting parts.

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

-   `test_add_returns_sum_for_positive_numbers` — clearer than `test_add_1`
-   `test_withdraw_fails_when_balance_is_zero` — instantly tells you what and why
-   `test_user_email_is_lowercased_after_save` — pinpoints the behaviour

When such a test fails in CI, the name alone tells you **what broke**, without reading the body. That saves hours of debugging in large projects.

## What's next?

The next article covers pytest's two superpowers: **fixtures** (shared test setup) and **parametrization** (one test, many inputs).

---

**Which statement about pytest basics is correct?**

