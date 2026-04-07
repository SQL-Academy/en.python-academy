# Tuples in Python

In this article, we'll get acquainted with tuples —
ordered and immutable collections of data in Python.

Tuples may seem very similar to lists, but there are important differences between them
that affect when and how they should be used.

## What is a tuple?

A tuple in Python is an ordered, immutable collection of elements that can be of different types. Simply put, it's like a list, but one that cannot be changed after creation.

Main properties of tuples:

-   **Ordered**: elements are stored in a specific order
-   **Immutable**: after creation, you cannot add, remove, or change elements
-   **Indexable**: elements can be accessed by their indices
-   **Allow duplicates**: they can contain repeated values
-   **Can contain different data types**: integers, strings, lists, etc.

## Creating tuples

There are several ways to create tuples in Python:

### 1. Using parentheses ()

```python
# Empty tuple
empty_tuple = ()

# Tuple with a single element (comma is required!)
single_item = (42,)
type(single_item)

# Without a comma, it's just a number: (42) == 42
single_item_num = (42)
type(single_item_num)

# Tuple of numbers
numbers = (1, 2, 3, 4, 5)

# Tuple with different data types
mixed = (1, "hello", True, 3.14)

# Nested tuples
nested = ((1, 2), ("a", "b"), (True, False))
```

### 2. Without parentheses (comma separation)

Python allows creating tuples even without parentheses, simply by listing elements separated by commas:

```python
# Creating a tuple without parentheses
coordinates = 10.5, 20.7, 30.9
type(coordinates)

# Unpacking a tuple
x, y, z = coordinates
print(x, y, z)
```

### 3. Using the tuple() constructor

```python
# Creating an empty tuple
empty_tuple = tuple()

# Converting a list to a tuple
list_to_tuple = tuple([1, 2, 3])
print(list_to_tuple)

# Converting a string to a tuple (each character becomes an element)
string_to_tuple = tuple("Python")
print(string_to_tuple)

# Converting a set to a tuple
set_to_tuple = tuple({1, 2, 3})
print(set_to_tuple)
```

## Accessing tuple elements

Accessing tuple elements is done the same way as in lists — through indices and slices:

### Indexing

```python
fruits = ("apple", "banana", "cherry", "date", "elderberry")

# Getting elements by index
first_fruit = fruits[0]
print(first_fruit)

# Negative indices for accessing from the end of the tuple
last_fruit = fruits[-1]
print(last_fruit)
```

### Slices

```python
fruits = ("apple", "banana", "cherry", "date", "elderberry")

# First three elements
first_three = fruits[:3]
print(first_three)

# From second to fourth
middle = fruits[1:4]
print(middle)

# Reversing the tuple
reversed_tuple = fruits[::-1]
print(reversed_tuple)
```

## Tuple methods

Since tuples are immutable, they have only two methods:

```python
fruits = ("apple", "banana", "cherry", "banana", "date")

# Counting the number of occurrences of an element
banana_count = fruits.count("banana")
print(banana_count)

# Finding the index of the first occurrence of an element
banana_index = fruits.index("banana")
print(banana_index)
```

## Operations with tuples

```python
# Finding the length
fruits = ("apple", "banana", "cherry")
print(len(fruits))

# Checking if an element exists
print("apple" in fruits)
print("mango" in fruits)

# Concatenation (combining) tuples
more_fruits = ("pear", "orange")
all_fruits = fruits + more_fruits
print(all_fruits)

# Repetition
repeated = fruits * 2
print(repeated)

# Unpacking
a, b, c = fruits
print(a, b, c)
```

## Immutability of tuples

It's important to understand that the immutability of tuples means that after creating a tuple you cannot:

-   Modify existing elements
-   Add new elements
-   Remove elements

```python
# Creating a tuple
coordinates = (10.5, 20.7, 30.9)

# These operations will raise a TypeError:
# coordinates[0] = 15.0     # Cannot modify an element
# coordinates.append(40.2)  # No append method
# coordinates.remove(20.7)  # No remove method
# del coordinates[1]        # Cannot delete an element

# But you can create a new tuple based on an existing one
new_coordinates = (15.0,) + coordinates[1:]
print(new_coordinates)
```

### Important note about nested mutable objects

```python
# Tuple containing a list
tuple_with_list = (1, 2, [3, 4])

# This works because we're modifying the list inside the tuple
tuple_with_list[2][0] = 30
print(tuple_with_list)

# But this will raise an error - cannot modify the tuple itself
# tuple_with_list[2] = [5, 6]  # TypeError
```

## Practical examples of using tuples

### 1. Returning multiple values from functions

```python
def get_user_info():
    name = "Anna"
    age = 30
    is_admin = True
    return name, age, is_admin

# Unpacking the result
user_name, user_age, user_is_admin = get_user_info()
print(f"Name: {user_name}, Age: {user_age}, Admin: {user_is_admin}")
```

### 2. Fixed data

```python
# Days of the week - a perfect example of an immutable sequence
DAYS_OF_WEEK = ("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday")

# Usage
today_index = 4  # Friday
print(f"Today is {DAYS_OF_WEEK[today_index]}")
```

## Comparison of lists and tuples

| Characteristic                | List                          | Tuple             |
| ----------------------------- | ----------------------------- | ----------------- |
| Syntax                        | `[1, 2, 3]`                   | `(1, 2, 3)`       |
| Mutability                    | Yes                           | No                |
| Methods                       | Many: append, remove, sort... | Only count, index |
| Performance                   | Slower                        | Faster            |
| Memory usage                  | More                          | Less              |
| Can be used as dictionary key | No                            | Yes               |
| Suitable for                  | Collections that can change   | Immutable data    |

## Common mistakes when working with tuples

1. **Forgotten comma in a single-element tuple**

    ```python
    # Incorrect (this is not a tuple, just a number in parentheses)
    not_a_tuple = (42)
    print(type(not_a_tuple))

    # Correct
    single_item_tuple = (42,)
    print(type(single_item_tuple))
    ```

2. **Attempting to modify a tuple**

    ```python
    coordinates = (10.5, 20.7, 30.9)

    # This will raise an error
    try:
        coordinates[0] = 15.0
    except TypeError as e:
        print(f"Error: {e}")
    ```

## Understanding check

**Which of the following statements about tuples in Python is true?**
