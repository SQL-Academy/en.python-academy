# Mocks and Stubs in Python: Isolating Tests with unittest.mock

In the previous articles, we mastered `pytest`, including its powerful fixtures and parametrization. However, when testing real applications, a common problem arises: our code depends on external systems (databases, APIs, file systems, time) or other complex components that are difficult or undesirable to use directly in tests.

This is where **test doubles**, particularly **mocks** and **stubs**, come to the rescue.

## The Problem of External Dependencies

Imagine you are testing a function that:

-   Sends an HTTP request to an external API (e.g., to get currency exchange rates).
-   Reads or writes data to a database.
-   Works with files on disk.
-   Depends on the current time.

Using these dependencies directly in unit tests is undesirable because:

-   **Slow**: Network requests or database operations can be lengthy.
-   **Unreliable**: The external service might be unavailable, or data in the DB might change.
-   **Hard to Control**: It's difficult to simulate specific responses or error states from external systems.
-   **Side Effects**: Tests might alter real data (e.g., create records in the DB or send actual emails).

> The goal of unit testing is to verify the logic of _your_ code in isolation. Test doubles allow replacing real dependencies with controlled simulations.

## Test Doubles: Stubs vs. Mocks

There are several types of test doubles, but stubs and mocks are the most commonly used.

-   **Stub**: A simple object that provides predefined ("canned") answers to method calls during a test. Stubs are used when the test needs some data from a dependency, but the dependency itself is not the object under test.

    -   _Example_: A stub for a weather service always returns `{"temperature": 25, "condition": "sunny"}`.

-   **Mock**: A more "intelligent" object. Mocks not only provide responses (like stubs) but also allow you to **verify how your code interacted with that dependency**. You can set expectations (e.g., which method should be called, with what arguments, how many times) and then check if these expectations were met.
    -   _Example_: A mock for an email sending service that allows verifying that the `send_email` method was called exactly once with the correct recipient address and subject line.

| Type     | Primary Purpose                                        | Verifies Interactions? | When to Use                                             |
| -------- | ------------------------------------------------------ | ---------------------- | ------------------------------------------------------- |
| **Stub** | Providing fixed data for the test                      | No                     | When the test needs simple data from a dependency       |
| **Mock** | Simulating behavior and **verifying** call correctness | Yes                    | When it's important to check how code uses a dependency |

## The unittest.mock Library

Python provides a powerful standard library, `unittest.mock`, for creating mocks, stubs, and other test doubles. It integrates perfectly with `pytest`.

Key components of `unittest.mock`:

-   `Mock` and `MagicMock`: Core classes for creating mock objects. `MagicMock` extends `Mock` by providing implementations for most magic methods (e.g., `__str__`, `__len__`).
-   `patch`: A powerful tool (decorator or context manager) for temporarily replacing objects in your code with mocks during test execution.

### Basic Usage of Mock

```python
# test_basic_mock.py
from unittest.mock import Mock

def process_payment(payment_service, amount):
    # print(f"Attempting to charge {amount}...") # Simplified by removing print
    success = payment_service.charge(amount)
    if success:
        # print("Payment successful") # Simplified
        payment_service.log_transaction(details=f"Charged {amount}")
        return "SUCCESS"
    else:
        # print("Payment error") # Simplified
        return "FAILED"

def test_process_payment_success():
    # 1. Create a mock for payment_service
    mock_payment_service = Mock()

    # 2. Configure the mock's behavior
    # The charge() method should return True
    mock_payment_service.charge.return_value = True

    # 3. Call the function under test with the mock
    result = process_payment(mock_payment_service, 100)

    # 4. Check the function's return value
    assert result == "SUCCESS"

    # 5. Verify interactions with the mock
    mock_payment_service.charge.assert_called_once_with(100) # Check charge was called once with 100
    mock_payment_service.log_transaction.assert_called_once_with(details="Charged 100") # Check log_transaction call

def test_process_payment_failure():
    mock_payment_service = Mock()
    mock_payment_service.charge.return_value = False # Simulate failed payment

    result = process_payment(mock_payment_service, 50)

    assert result == "FAILED"
    mock_payment_service.charge.assert_called_once_with(50)
    mock_payment_service.log_transaction.assert_not_called() # Ensure log_transaction was not called
```

In these examples, `mock_payment_service.charge` and `mock_payment_service.log_transaction` automatically become mocks upon first access. The `return_value` attribute sets what the mock method call will return. Methods like `assert_called_once_with()` and `assert_not_called()` verify how the mock was used.

### patch: Replacing Objects on the Fly

`patch` allows you to replace objects within a specific module with mocks for the duration of a test. This is very useful when you cannot easily pass the mock as an argument (e.g., if the object is created inside the function under test or imported globally).

`patch` can be used as a decorator or a context manager.

```python
# test_patch_example.py
from unittest.mock import patch, Mock

# --- Code under test (usually in another file) ---
import requests
def get_external_data(item_id):
    # This function makes a real network request
    print(f"\nCalling requests.get for {item_id}...") # Keep print to show it WON'T run in the test
    response = requests.get(f"https://api.example.com/items/{item_id}")
    if response.status_code == 200:
        return response.json()
    return None
# --- End of code under test ---

#### Using patch as a decorator

@patch('__main__.requests.get') # Patch requests.get in the current module where it's used
def test_get_external_data_with_decorator(mock_requests_get):
    # Configure the mock passed into mock_requests_get
    print("\nRunning test_get_external_data_with_decorator")
    mock_response = Mock()
    mock_response.status_code = 200
    mock_response.json.return_value = {"id": 1, "name": "Test Item"}
    mock_requests_get.return_value = mock_response

    data = get_external_data(1)

    assert data == {"id": 1, "name": "Test Item"}
    mock_requests_get.assert_called_once_with("https://api.example.com/items/1")

#### Using patch as a context manager

def test_get_external_data_with_context_manager():
    print("\nRunning test_get_external_data_with_context_manager")
    with patch('__main__.requests.get') as mock_requests_get_cm:
        mock_response = Mock()
        mock_response.status_code = 404
        mock_requests_get_cm.return_value = mock_response

        data = get_external_data(2)

        assert data is None
        mock_requests_get_cm.assert_called_once_with("https://api.example.com/items/2")

#### Patching object methods with patch.object

# --- Code under test ---
class ReportGenerator:
    def _get_user_stats(self, user_id):
        # Simulate a complex/slow call
        raise NotImplementedError("Don't call the real method in tests!")

    def generate_report(self, user_id):
        stats = self._get_user_stats(user_id)
        return f"User {user_id}: Logins - {stats['logins']}, Spent - {stats['spent']}"
# --- End of code under test ---

@patch.object(ReportGenerator, '_get_user_stats') # Patch a method of a specific class
def test_generate_report_patches_object_method(mock_get_stats):
    print("\nRunning test_generate_report_patches_object_method")
    mock_get_stats.return_value = {"logins": 10, "spent": 50} # Simplified data for the test

    generator = ReportGenerator()
    report = generator.generate_report(user_id=123)

    assert report == "User 123: Logins - 10, Spent - 50"
    mock_get_stats.assert_called_once_with(123)
```

**Important when using `patch`**: The target string for patching should be where the object is _looked up_ (imported and used), not necessarily where it's defined.

### side_effect: Simulating Errors and Sequential Calls

The `side_effect` attribute of a mock allows simulating more complex behavior:

-   **Raising an exception**: Assigning an exception (e.g., `ConnectionError`) to `side_effect` will make the mock raise that exception when called.
-   **Returning different values on sequential calls**: Assigning an iterable (e.g., a list) to `side_effect` will make the mock return the next item from the iterable on each call.
-   **Calling a function**: Assigning a function to `side_effect` will cause that function to be called instead of the mock.

```python
from unittest.mock import Mock

# 1. Simulate an exception
mock_api = Mock()
mock_api.connect.side_effect = ConnectionError("Failed to connect to API")

try:
    mock_api.connect()
except ConnectionError as e:
    print(f"Caught expected error: {e}")

# 2. Sequential return values
mock_db_reader = Mock()
mock_db_reader.read_row.side_effect = [
    {"id": 1, "data": "A"},
    {"id": 2, "data": "B"},
    None # Simulate end of data
]

print(mock_db_reader.read_row()) # Returns {"id": 1, "data": "A"}
print(mock_db_reader.read_row()) # Returns {"id": 2, "data": "B"}
print(mock_db_reader.read_row()) # Returns None

# 3. Execute a function
def custom_side_effect(*args, **kwargs):
    user_id = kwargs.get("user_id", 0)
    print(f"-> custom_side_effect called for user_id={user_id}")
    return "Admin" if user_id == 1 else "Guest"

mock_user_service = Mock()
mock_user_service.get_user_type.side_effect = custom_side_effect

print(mock_user_service.get_user_type(user_id=1))
print(mock_user_service.get_user_type(user_id=2))
```

### Usage with pytest and the mocker Fixture

While `unittest.mock` is part of the standard library, the `pytest-mock` plugin is often used for more convenient integration with `pytest`. It provides the `mocker` fixture, which is a wrapper around `unittest.mock.patch` and simplifies some scenarios.

```bash
# pip install pytest-mock (if not already installed)
```

```python
# test_pytest_mocker.py
# (assuming get_external_data is defined as in the previous patch example)

# --- Code from module_to_test.py for self-sufficiency ---
import requests
def get_external_data(item_id):
    response = requests.get(f"https://api.example.com/items/{item_id}")
    if response.status_code == 200:
        return response.json()
    return None
# --- End of code from module_to_test.py ---

def test_get_external_data_with_mocker(mocker): # mocker is the pytest-mock fixture
    # Use mocker.patch instead of unittest.mock.patch
    mock_requests_get = mocker.patch('__main__.requests.get')

    mock_response = Mock() # Can still use unittest.mock.Mock directly
    mock_response.status_code = 200
    mock_response.json.return_value = {"id": 10, "name": "Mocked Item"}
    mock_requests_get.return_value = mock_response

    data = get_external_data(10)
    assert data == {"id": 10, "name": "Mocked Item"}
    mock_requests_get.assert_called_once_with("https://api.example.com/items/10")

# mocker also has convenience methods like mocker.stub()
class EmailSender:
    def send(self, to, body):
        # Real email sending
        raise NotImplementedError("Don't send real emails in tests!")

def test_email_sending_logic(mocker):
    sender_instance = EmailSender()
    # mocker.patch.object() is similar to unittest.mock.patch.object()
    mock_send_method = mocker.patch.object(sender_instance, 'send')
    mock_send_method.return_value = True # Configure send to return True

    # Code under test that uses sender_instance.send(...)
    # For example:
    # result = process_user_notification(user_id=1, email_sender=sender_instance)
    # assert result is True

    # For the example, just call it directly
    assert sender_instance.send("test@example.com", "Hello") == True
    mock_send_method.assert_called_once_with("test@example.com", "Hello")
```

The `mocker` fixture provides convenient access to `patch` functionality without needing to import `patch` directly in every test file.

## Best Practices for Using Mocks and Stubs

1.  **Mock Boundaries, Not Internals**: Try to mock only what's at the boundary of your system or module (external APIs, direct DB access, file system). Avoid mocking too many internal details of your own code, otherwise, tests become brittle and test the implementation rather than the behavior.
2.  **Test Behavior, Not Implementation**: Mocks help verify that your code _correctly uses_ its dependencies, not how those dependencies are implemented internally.
3.  **One Mock per Behavior**: Aim for a single mock to simulate one specific behavior or aspect of a dependency.
4.  **Keep Mocks Simple**: The simpler the mock, the easier the test is to understand.
5.  **Use `patch` Judiciously**: Use `patch` thoughtfully. Overusing `patch` can make tests complex to understand and maintain, as it becomes unclear what code is actually running.
6.  **Prefer Dependency Injection**: If possible, design your code so dependencies can be easily passed into functions or classes (e.g., via constructor or method arguments). This often simplifies testing and reduces the need for `patch`.

## What's Next?

Now that you know how to isolate dependencies using mocks and stubs, your tests will become more reliable, faster, and focused on verifying specific logic.

In the next article, we will explore Python's built-in `unittest` framework, which also heavily utilizes mocking concepts.

---

**Which statement about mocks and stubs in Python is correct?**
