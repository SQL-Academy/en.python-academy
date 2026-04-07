# Complex Data Types in Python

We've already familiarized ourselves with simple (scalar) data types in Python. Now it's time to move on to more powerful tools — complex (composite) data types, which allow us to store and process collections of values.

## What are complex data types?

Unlike simple data types (numbers, strings, boolean values) that store a single value, complex types can contain **multiple values** of various types under one name. They are similar to containers or collections where you can organize and store related data.

Python offers four main complex data types:

1. **Lists** (list) — ordered, mutable collections of elements
2. **Tuples** (tuple) — ordered, immutable collections of elements
3. **Sets** (set) — unordered collections of unique elements
4. **Dictionaries** (dict) — collections of key-value pairs

Let's look at a brief example of each type:

```python
# List (ordered, mutable)
fruits = ["apple", "banana", "cherry"]

# Tuple (ordered, immutable)
coordinates = (10.5, 20.7, 30.9)

# Set (unordered, unique elements only)
unique_numbers = {1, 2, 3, 4, 5}

# Dictionary (key-value pairs)
person = {"name": "Alex", "age": 30, "city": "New York"}
```

## Comparison of complex data types

Key differences between the main complex data types:

| Data Type             | Syntax             | Main Properties                                                  | When to Use                                              |
| --------------------- | ------------------ | ---------------------------------------------------------------- | -------------------------------------------------------- |
| **List (list)**       | `[1, 2, 3]`        | ✅ Mutable<br/> ✅ Ordered<br/> ✅ Index access                  | When order matters and you need to modify the collection |
| **Tuple (tuple)**     | `(1, 2, 3)`        | ❌ Immutable<br/> ✅ Ordered<br/> ✅ Index access                | To protect data from changes (coordinates, constants)    |
| **Set (set)**         | `{1, 2, 3}`        | ✅ Mutable<br/> ❌ Unordered<br/> ❌ No indexes                  | When you need only unique elements                       |
| **Dictionary (dict)** | `{"key": "value"}` | ✅ Mutable<br/> ✅ Ordered (since Python 3.7)<br/> ✅ Key access | For connecting values with unique keys                   |

## When to use different data types?

The choice of an appropriate data type depends on the task:

-   **List (list)** — when the order of elements is important and the collection may change

    ```python
    # Daily to-do list
    todo_list = ["Buy groceries", "Walk the dog", "Write code"]
    ```

-   **Tuple (tuple)** — when data should not change and order is important

    ```python
    # Point coordinates in 3D space
    point_3d = (10.5, 8.3, 9.1)
    ```

-   **Set (set)** — when you only need unique elements and order doesn't matter

    ```python
    # Unique user names
    unique_users = {"alice", "bob", "charlie"}
    ```

-   **Dictionary (dict)** — when data is organized as key-value pairs
    ```python
    # User information
    user = {"username": "alex123", "email": "alex@example.com", "active": True}
    ```

## Converting between types

Python makes it easy to convert data from one complex type to another:

```python
# Create a list
my_list = [1, 2, 3, 3, 4, 5]

# Convert list to tuple
my_tuple = tuple(my_list)  # (1, 2, 3, 3, 4, 5)

# Convert list to set (duplicates will be removed)
my_set = set(my_list)  # {1, 2, 3, 4, 5}

# Create a dictionary from a list of pairs
pairs = [("a", 1), ("b", 2), ("c", 3)]
my_dict = dict(pairs)  # {"a": 1, "b": 2, "c": 3}
```

## Understanding Check

Let's check how well you've understood the basic complex data types:

**Which data type is best suited for storing unique elements when order doesn't matter?**


## Conclusion

Complex data types in Python are powerful tools that allow you to organize,
store, and process collections of values.

Choosing the right data type for a specific task can significantly simplify your code and make it more efficient.

In the following articles, we'll examine each of these types in detail, study their methods, and
learn how to work with them.

Let's start by exploring [lists](/guide/lists), the most flexible and frequently used complex data type in Python!
