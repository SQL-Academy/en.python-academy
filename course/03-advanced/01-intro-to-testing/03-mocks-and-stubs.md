---
meta:
    title: "Mocks and stubs: isolating tests from external dependencies"
    description: "Why you need mocks and stubs in Python, how to use unittest.mock.patch, and basic unittest.mock.Mock usage."
---

# Mocks and stubs: isolating tests

The same payment test sometimes passes instantly, sometimes hangs for ten seconds, and occasionally fails with a timeout — though nobody touched the code. The reason: every run it reaches out to the payment provider's real sandbox, which is fast one minute and slow the next. In effect the test is checking someone else's server, not **your logic**.

That's what happens when code under test talks to the outside world: HTTP requests, databases, files, time. The network drops, the DB is slow, time is uncontrollable — so for the duration of the test the dependency has to be replaced with something.

The fix is a **test double**: slip a fake object into your code that behaves the way you want. The fake has two main roles:

- **Stub** returns canned answers, no checks needed.
- **Mock** does the same and also records calls (with which arguments, how many times) so the test can verify them.

Both are built with one tool — `unittest.mock` from the standard library. The name is historical: the module shipped alongside the built-in `unittest` framework (we'll get to it one article from now), but it's tied to pytest just as much — nothing to install, it works as is.

![Illustration: on the left a Mock with a green check and charge.return_value = True, passed as an argument into the test process_payment(service, 100) in the centre, while the real payment_service on the right is crossed out in red (slow, unreliable)](https://python-academy.org/static/guidePage/mocks-and-stubs/mock-substitution-en.webp "A Mock is passed as an argument in place of the real service")

## The Mock object

Simplest case: create a `Mock` and configure what its methods should return.

```python
from unittest.mock import Mock

def process_payment(service, amount):
    if service.charge(amount):
        service.log(f"charged {amount}")
        return "OK"
    return "FAIL"

def test_payment_success():
    service = Mock()
    service.charge.return_value = True       # configure the mock

    result = process_payment(service, 100)

    assert result == "OK"
    service.charge.assert_called_once_with(100)
    service.log.assert_called_once_with("charged 100")
```

`process_payment` takes `service` as an argument. In production code that's the real payment service; in the test we pass a Mock through the same argument — this is the point of "dependency through a parameter". Then:

- `Mock()` creates an object where **any** attribute or method exists automatically.
- `service.charge.return_value = True` says "when the test calls `service.charge(...)`, return `True`".
- `assert_called_once_with(100)` checks: "method `charge` was called exactly once with the argument `100`". That's the difference between a Mock and a Stub: the test doesn't just get data, it verifies **how** the code used the dependency.

A mock is easier to touch once than to describe. Run this — and look at the last line: a `Mock` even has a method we never defined:

```python
from unittest.mock import Mock

m = Mock()
m.charge.return_value = True

print(m.charge(100))
<output>
True
</output>
print(m.charge.call_args)
<output>
call(100)
</output>
print(type(m.method_that_does_not_exist).__name__)
<output>
Mock
</output>
```

## patch: replace an already existing object

`Mock()` is good when the dependency is passed into the function as an argument. But often code uses an imported object directly (e.g. `requests.get`). Then you need `patch`: it temporarily swaps something in the code for a mock:

```python
from unittest.mock import patch, Mock

# code under test
import requests

def get_user(user_id):
    response = requests.get(f"https://api.example.com/users/{user_id}")
    if response.status_code == 200:
        return response.json()
    return None

# test
@patch("requests.get")
def test_get_user(mock_get):
    # configure what requests.get(...) returns
    mock_response = Mock()
    mock_response.status_code = 200
    mock_response.json.return_value = {"id": 1, "name": "Anna"}
    mock_get.return_value = mock_response

    user = get_user(1)

    assert user == {"id": 1, "name": "Anna"}
    mock_get.assert_called_once_with("https://api.example.com/users/1")
```

A few things to note:

- `@patch("requests.get")` swaps `requests.get` for a mock only for the duration of the test. After the test, everything is restored.
- The `mock_get` argument (any name works) is the auto-created mock that replaced the original. You configure behaviour and check calls on it.
- `response.json()` is a **method** (with parentheses), so we configure `mock_response.json.return_value`. The nested mock `json` has its own `return_value`. Any attribute or method of a mock is itself a mock — it chains.

`patch` also works as a **context manager** (`with patch(...) as mock_get:`), handy when the swap is needed only for part of the test.

## The main pitfall: which path to put in patch

The most common mistake with `patch` is the path. The rule: **patch where the object is used, not where it's defined**.

Say our `get_user` lives in `app.py`. If the code imports the whole module:

```python
# app.py
import requests

def get_user(user_id):
    return requests.get(f"https://api.example.com/users/{user_id}")
```

Then the test patches `"requests.get"` and it works, because `app.py` accesses the function through the `requests` module itself:

```python
@patch("requests.get")   # OK
def test_get_user(mock_get):
    ...
```

But if the code uses `from requests import get`:

```python
# app.py
from requests import get

def get_user(user_id):
    return get(f"https://api.example.com/users/{user_id}")
```

Then patching `"requests.get"` is **useless**. After the `from ... import`, `app.py` has its own local reference `get` that points at the original function. A patch on the `requests` module doesn't touch it. You have to patch at the usage site:

```python
@patch("app.get")    # patch where get is called from
def test_get_user(mock_get):
    ...
```

Mnemonic: **"patch where it's looked up"** — wherever the code under test looks for the function, that's where you patch.

## side_effect: simulating errors

Sometimes you need to check that the code reacts correctly to a dependency failure (e.g. a `ConnectionError`). Mocks support that via `side_effect`:

```python
from unittest.mock import Mock

mock_api = Mock()
mock_api.connect.side_effect = ConnectionError("network down")

# now mock_api.connect() raises ConnectionError
```

The same `side_effect` also accepts a function or a list of values (for sequential calls returning different things), but those are rarer cases.

## The main rule

Mocks are convenient but tricky: it's easy to start mocking the **internals** of your own code, and then your tests check implementation instead of behaviour. Any refactor breaks them even though the code still works.

Rule: **mock the boundaries of the system**: external APIs, the DB, the filesystem, time. Test your own code directly, without mocks.

## Understanding check

**What is true about mocks and stubs in Python?**

1. A Stub verifies that methods were called with the right arguments — Stubs just return canned data. Verifying calls is the job of mocks.

2. **Correct answer:** patch from unittest.mock temporarily replaces an object with a mock during the test — Once the test finishes, patch puts the original object back.

3. side_effect is only for returning sequential values — side_effect can also raise exceptions (e.g. ConnectionError) or call an arbitrary function.

4. In patch you give the path where the object is defined, not where it is used — The opposite: you patch where the object is looked up at call time — the path in the module that uses it, not where it was originally defined. This is the most common mistake with patch.

Next: the built-in **`unittest`** module, classic xUnit style with `TestCase` classes and `setUp`/`tearDown`. It's the alternative to pytest you'll meet in legacy code.
