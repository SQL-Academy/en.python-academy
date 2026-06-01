# pytest: fixtures and parametrization

In the previous article we wrote simple test functions. Now we'll cover two things that keep tests from turning into copy-paste: **fixtures** (shared setup for many tests) and **parametrization** (one test, many inputs).

## Fixtures: shared setup

Tests often need the same prepared state: a user with filled-in fields, an open object, a test database. A fixture is a helper function that returns that state and gets passed into tests by name. The `@pytest.fixture` decorator tells pytest "this function is a fixture, call it for tests that request it":

```python
import pytest

@pytest.fixture
def alice():
    return {"id": 1, "name": "Alice", "email": "alice@example.com"}

def test_user_has_email(alice):         # fixture name in argument
    assert "@" in alice["email"]

def test_user_id_is_int(alice):
    assert isinstance(alice["id"], int)
```

pytest sees that `test_user_has_email` requests the `alice` fixture, calls it, and passes the result into the test. If a test doesn't need the fixture, just don't include it in the arguments. The main convenience: you describe the setup once and reuse it in dozens of tests via one argument.

## yield: setup and teardown

When you need not only to prepare something but also to **clean up after the test** (close a connection, delete a temp file), use `yield` instead of `return`:

```python
import os
import pytest

@pytest.fixture
def temp_file():
    path = "temp_test_file.txt"
    with open(path, "w") as f:
        f.write("hello")

    yield path           # test gets path

    os.remove(path)       # runs after the test

def test_read(temp_file):
    with open(temp_file) as f:
        assert f.read() == "hello"
```

Code **before** `yield` runs before the test (setup); code **after** runs after the test (teardown). Works even if the test fails.

## Scope: how often to recreate

By default, a fixture runs **per test**. If setup is expensive (open a DB, load a big file), tell pytest to do it "once per session" with `scope="session"`:

```python
@pytest.fixture(scope="session")
def db_connection():
    print("\nconnecting to DB (one time)")
    conn = {"status": "connected"}
    yield conn
    print("\nclosing connection")
```

In practice, 95% of the time you'll use `function` (default, one instance per test) and `session` (one per run). There are also `class` and `module` for special cases, not needed to get started.

## conftest.py: sharing fixtures

If a fixture is needed in multiple files, put it in `conftest.py` next to your tests. No import needed, pytest finds it automatically:

<CodeProject
    defaultFile="test_api.py"
    files={[
        {
            path: 'conftest.py',
            content: `import pytest

@pytest.fixture(scope="session")
def app_config():
    return {"api_url": "https://test.example.com", "timeout": 5}
`,
        },
        {
            path: 'test_api.py',
            content: `def test_api_url(app_config):     # fixture from conftest.py
    assert "example.com" in app_config["api_url"]
`,
        },
    ]}
/>

That's the standard way to share fixtures across the tests of a project.

## Parametrization: one test, many inputs

When you need to check a function against 5–10 input sets, don't write 10 near-identical tests. Use `@pytest.mark.parametrize`:

```python
import pytest

def get_discount(age, is_member):
    if age >= 65:
        return 0.15
    if is_member:
        return 0.10
    if age < 18:
        return 0.05
    return 0.0

@pytest.mark.parametrize(
    "age, is_member, expected",
    [
        (70, False, 0.15),
        (30, True,  0.10),
        (16, False, 0.05),
        (25, False, 0.0),
        (65, True,  0.15),
    ],
)
def test_get_discount(age, is_member, expected):
    assert get_discount(age, is_member) == expected
```

Note the first argument of `parametrize`: it's a single **string** with parameter names separated by commas (`"age, is_member, expected"`), not a tuple. That's a pytest-specific convention. After it comes the list of tuples, one tuple per test run.

pytest runs this test 5 times, once per row. In the output they appear as separate tests:

```text
test_discount.py::test_get_discount[70-False-0.15] PASSED
test_discount.py::test_get_discount[30-True-0.1]   PASSED
...
```

If one case fails, the test name shows its parameters, so it's immediately clear which combination broke.

## What's next?

Next up: **mocks and stubs**, how to isolate tests from external dependencies (DB, API, time) so they stay fast and predictable.

---

**What is true about fixtures and parametrization in pytest?**

