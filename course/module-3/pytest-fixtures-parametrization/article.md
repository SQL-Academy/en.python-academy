# pytest: Fixtures, Parametrization, and Advanced Tests

In the previous article, we got acquainted with the basics of `pytest`: we learned how to write and run simple tests. Now it's time to delve into two of the most powerful and frequently used concepts in `pytest` that make it so flexible and convenient: **fixtures** and **parametrization**.

## Fixtures: Preparing and Managing the Test Environment ⚙️

Often, to run a test, you need to perform some preliminary actions: prepare data, set up a test object, establish a database connection, etc. After the test, you might need to perform cleanup: delete temporary files, close connections.

> **Fixtures** in `pytest` are functions that serve to set up and provide data or objects needed for your tests. They help make tests cleaner, more structured, and avoid code duplication.

### Creating a Simple Fixture

A fixture is defined using the `@pytest.fixture` decorator.

```python
# test_fixtures_example.py
import pytest

@pytest.fixture
def sample_list():
    print("\n(Fixture sample_list: creating list)")
    return [1, 2, 3, 4, 5]

@pytest.fixture
def sample_dict():
    print("\n(Fixture sample_dict: creating dictionary)")
    return {"name": "Alice", "age": 30}

def test_list_length(sample_list): # Fixture name is passed as an argument
    print("(Test test_list_length)")
    assert len(sample_list) == 5

def test_dict_name(sample_dict): # Another fixture
    print("(Test test_dict_name)")
    assert sample_dict["name"] == "Alice"

def test_list_and_dict_usage(sample_list, sample_dict):
    print("(Test test_list_and_dict_usage)")
    assert len(sample_list) > 0
    assert "age" in sample_dict
```

If you run `pytest -v -s` (the `-s` flag is needed to see the `print` outputs from fixtures and tests):

-   `pytest` will execute each fixture before the test that requests it.
-   The result of the fixture execution (what it returns) is passed into the test as an argument.

### Fixture Teardown: yield for Setup and Teardown

If you need to perform actions _after_ the test has finished (e.g., close a database connection or delete a temporary file), use `yield` in the fixture.

```python
# test_fixture_yield.py
import pytest
import os

@pytest.fixture
def temp_file_fixture():
    file_path = "temp_test_file.txt"
    print(f"\n(Fixture: creating file {file_path})")
    with open(file_path, "w") as f:
        f.write("Hello, pytest!")

    yield file_path # The test receives this value

    print(f"\n(Fixture: deleting file {file_path})")
    os.remove(file_path)

def test_read_temp_file(temp_file_fixture):
    file_path = temp_file_fixture # Get the file path from the fixture
    print(f"(Test: reading file {file_path})")
    with open(file_path, "r") as f:
        content = f.read()
    assert content == "Hello, pytest!"
```

The code before `yield` runs before the test (setup), and the code after `yield` runs after the test (teardown).

### Fixture Scopes (scope)

By default, a fixture runs for every test function that requests it. This behavior can be changed using the `scope` parameter in the `@pytest.fixture` decorator.

Available scopes:

-   `function` (default): Fixture runs once per test function.
-   `class`: Fixture runs once per test class.
-   `module`: Fixture runs once per module.
-   `package`: Fixture runs once per package (Python 3).
-   `session`: Fixture runs once for the entire test session.

```python
# test_scopes.py
import pytest

@pytest.fixture(scope="session")
def session_db_connection():
    print("\n(Fixture session_db_connection: establishing DB connection - ONCE PER SESSION)")
    connection = {"status": "connected"} # Simulate connection
    yield connection
    print("\n(Fixture session_db_connection: closing DB connection - ONCE PER SESSION)")

@pytest.fixture(scope="module")
def module_data_setup(session_db_connection):
    # This fixture depends on session_db_connection
    assert session_db_connection["status"] == "connected"
    print("\n(Fixture module_data_setup: setting up module data - ONCE PER MODULE)")
    data = {"module_id": 123}
    yield data
    print("\n(Fixture module_data_setup: cleaning up module data)")

class TestUserOperations:
    def test_user_create(self, module_data_setup, session_db_connection):
        print("(Test test_user_create)")
        assert session_db_connection["status"] == "connected"
        assert module_data_setup["module_id"] == 123

    def test_user_view(self, module_data_setup, session_db_connection):
        print("(Test test_user_view)")
        assert session_db_connection["status"] == "connected"
        assert module_data_setup["module_id"] == 123

def test_another_operation(module_data_setup, session_db_connection):
    print("(Test test_another_operation in the same module)")
    assert session_db_connection["status"] == "connected"
    assert module_data_setup["module_id"] == 123
```

Choosing the correct scope helps optimize test execution by avoiding unnecessary setup repetitions.

### Shared Fixtures: conftest.py

If you have fixtures that you want to use across multiple test files, you can define them in a special file named `conftest.py`. `pytest` automatically discovers this file.

-   The `conftest.py` file should be located in the test directory or a parent directory.
-   Fixtures defined in `conftest.py` become available to all tests in that directory and its subdirectories without needing to be imported.

**Example:**

Contents of `conftest.py` (in the root test folder or a subfolder):

```python
# conftest.py
import pytest

@pytest.fixture(scope="session")
def global_app_config():
    print("\n(conftest.py: Loading global application config - ONCE PER SESSION)")
    return {"api_url": "https://test.api.example.com", "timeout": 5}
```

Contents of `test_module_one.py`:

```python
# test_module_one.py
def test_api_url(global_app_config): # Fixture from conftest.py is available here
    print(f"(Test test_api_url: using {global_app_config['api_url']})")
    assert "example.com" in global_app_config["api_url"]
```

### Automatic Fixture Usage (autouse)

Sometimes, a fixture needs to run for all tests within a specific scope, even if the tests don't explicitly request it. The `autouse=True` parameter is used for this.

```python
# conftest.py
import pytest

@pytest.fixture(autouse=True, scope="session")
def print_start_end_session():
    print("\n--- STARTING TEST SESSION ---")
    yield
    print("\n--- ENDING TEST SESSION ---")

@pytest.fixture(autouse=True, scope="function")
def track_test_duration():
    import time
    start_time = time.time()
    yield
    duration = time.time() - start_time
    print(f"(Test duration: {duration:.4f} sec.)")
```

Use `autouse` with caution, as it can make test dependencies less explicit.

## Parametrization: One Test, Many Runs 🔄

Often, you need to test the same logic with different sets of input data and expected results. Instead of writing multiple nearly identical tests, `pytest` offers **parametrization** using the `@pytest.mark.parametrize` marker.

```python
# test_parametrize_example.py
import pytest

def get_discount(age, is_member):
    if age >= 65:
        return 0.15 # 15% discount for seniors
    if is_member:
        return 0.10 # 10% discount for members
    if age < 18:
        return 0.05 # 5% discount for juniors
    return 0.0

@pytest.mark.parametrize(
    "age, is_member, expected_discount", # Argument names in the test function
    [
        (70, False, 0.15),      # Test case 1
        (30, True, 0.10),       # Test case 2
        (16, False, 0.05),      # Test case 3
        (25, False, 0.0),       # Test case 4
        (65, True, 0.15),       # Senior member still gets senior discount
        pytest.param(10, True, 0.05, id="JuniorMember"), # Example with a test ID
    ]
)
def test_get_discount_parametrized(age, is_member, expected_discount):
    assert get_discount(age, is_member) == expected_discount
```

In this example:

-   The `test_get_discount_parametrized` test will run multiple times, once for each tuple of values in the list.
-   `pytest.param(...)` can be used to assign a custom ID to a test case, improving output readability with many parameters.

### Combining Fixtures and Parametrization

Fixtures and parametrization can be used together to create very flexible test scenarios.

```python
# test_fixtures_and_parametrize.py
import pytest

@pytest.fixture
def user_profile_factory():
    def _create_profile(name, role="viewer"):
        return {"username": name, "role": role, "permissions": []}
    return _create_profile

@pytest.mark.parametrize(
    "role_to_assign, expected_permission_count",
    [
        ("admin", 5),
        ("editor", 3),
        ("viewer", 1)
    ]
)
def test_user_permissions_after_role_assignment(
    user_profile_factory,
    role_to_assign,
    expected_permission_count
):
    profile = user_profile_factory(name="testuser")

    # Simulate permission assignment logic based on role
    if role_to_assign == "admin":
        profile["permissions"] = ["read", "write", "delete", "manage_users", "publish"]
    elif role_to_assign == "editor":
        profile["permissions"] = ["read", "write", "publish"]
    elif role_to_assign == "viewer":
        profile["permissions"] = ["read"]

    profile["role"] = role_to_assign

    assert profile["role"] == role_to_assign
    assert len(profile["permissions"]) == expected_permission_count
```

## What's Next?

Fixtures and parametrization are the bread and butter of effective testing with `pytest`. By mastering them, you can write clean, maintainable, and comprehensive tests.

In the next article, we will look at how to isolate our tests from external dependencies using mocks and stubs.

---

**Which statement about fixtures and parametrization in `pytest` is correct?**
