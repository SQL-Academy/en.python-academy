---
meta:
    title: "Tuples in Python"
    description: "Detailed description of tuples in Python: creation, methods for working with tuples, comparison with lists, and practical usage examples."
---

# Tuples in Python

When data has a **fixed shape** (coordinates, an RGB colour, a function return value made of several parts), that's a case for a tuple, not a list.

## What is a tuple?

A tuple (`tuple`) in Python is an ordered, immutable collection of elements that can be of different types. Simply put, it's like a list, only you can't change it after creation.

Main properties of tuples:

- **Ordered**: elements are stored in a specific order
- **Immutable**: after creation, you cannot add, remove, or change elements
- **Indexable**: elements can be accessed by their indices
- **Allow duplicates**: they can contain repeated values
- **Can contain different data types**: integers, strings, lists, etc.

## Creating tuples

There are several ways to create tuples in Python:

### Using parentheses ()

```python
# Empty tuple
empty_tuple = ()

# Tuple with a single element (comma is required!)
single_item = (42,)
print(type(single_item))
<output>
<class 'tuple'>
</output>

# Without a comma, it's just a number: (42) == 42
single_item_num = (42)
print(type(single_item_num))
<output>
<class 'int'>
</output>

# Tuple of numbers
numbers = (1, 2, 3, 4, 5)

# Tuple with different data types
mixed = (1, "hello", True, 3.14)

# Nested tuples
nested = ((1, 2), ("a", "b"), (True, False))
```

### Without parentheses (comma separation)

Python allows creating tuples even without parentheses, simply by listing elements separated by commas:

```python
# Creating a tuple without parentheses
coordinates = 10.5, 20.7, 30.9
print(type(coordinates))
<output>
<class 'tuple'>
</output>
```

### Using the tuple() constructor

```python
# Creating an empty tuple
empty_tuple = tuple()

# Converting a list to a tuple
list_to_tuple = tuple([1, 2, 3])
print(list_to_tuple)
<output>
(1, 2, 3)
</output>

# Converting a string to a tuple (each character becomes an element)
string_to_tuple = tuple("Python")
print(string_to_tuple)
<output>
('P', 'y', 't', 'h', 'o', 'n')
</output>
```

## Accessing tuple elements

Accessing tuple elements is done the same way as in lists — through indices and slices:

### Indexing

```python
fruits = ("apple", "banana", "cherry", "date", "elderberry")

# Getting elements by index
first_fruit = fruits[0]
print(first_fruit)
<output>
apple
</output>

# Negative indices for accessing from the end of the tuple
last_fruit = fruits[-1]
print(last_fruit)
<output>
elderberry
</output>
```

### Slices

```python
fruits = ("apple", "banana", "cherry", "date", "elderberry")

# First three elements
first_three = fruits[:3]
print(first_three)
<output>
('apple', 'banana', 'cherry')
</output>

# From second to fourth
middle = fruits[1:4]
print(middle)
<output>
('banana', 'cherry', 'date')
</output>

# Reversing the tuple
reversed_tuple = fruits[::-1]
print(reversed_tuple)
<output>
('elderberry', 'date', 'cherry', 'banana', 'apple')
</output>
```

## Tuple methods

Since tuples are immutable, they have only two methods:

```python
fruits = ("apple", "banana", "cherry", "banana", "date")

# Counting the number of occurrences of an element
banana_count = fruits.count("banana")
print(banana_count)
<output>
2
</output>

# Finding the index of the first occurrence of an element
banana_index = fruits.index("banana")
print(banana_index)
<output>
1
</output>
```

## Operations with tuples

```python
# Finding the length
fruits = ("apple", "banana", "cherry")
print(len(fruits))
<output>
3
</output>

# Checking if an element exists
print("apple" in fruits)
<output>
True
</output>
print("mango" in fruits)
<output>
False
</output>

# Concatenation (combining) tuples
more_fruits = ("pear", "orange")
all_fruits = fruits + more_fruits
print(all_fruits)
<output>
('apple', 'banana', 'cherry', 'pear', 'orange')
</output>

# Repetition
repeated = fruits * 2
print(repeated)
<output>
('apple', 'banana', 'cherry', 'apple', 'banana', 'cherry')
</output>

# Unpacking
a, b, c = fruits
print(a, b, c)
<output>
apple banana cherry
</output>
```

## Comparing tuples

Tuples are compared **element by element**, left to right, until the first difference:

```python
print((1, 2) < (1, 3))
<output>
True
</output>
print((2, 0) < (1, 9))
<output>
False
</output>
```

In the first case the first elements are equal (`1 == 1`), so Python moves on to the second: `2 < 3`, so the whole tuple is smaller. In the second, `2 > 1` is enough on its own.

This lets you sort a list of tuples by several fields with one call:

```python
people = [("Bob", 30), ("Anna", 25), ("Anna", 30)]
print(sorted(people))
<output>
[('Anna', 25), ('Anna', 30), ('Bob', 30)]
</output>
```

The sort first compares the first element (name); on a tie it falls back to the second (age).

## Immutability of tuples

It's important to understand that the immutability of tuples means that after creating a tuple you cannot:

- Modify existing elements
- Add new elements
- Remove elements

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
<output>
(15.0, 20.7, 30.9)
</output>
```

### Important note about nested mutable objects

```python
# Tuple containing a list
tuple_with_list = (1, 2, [3, 4])

# This works because we're modifying the list inside the tuple
tuple_with_list[2][0] = 30
print(tuple_with_list)
<output>
(1, 2, [30, 4])
</output>

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
<output>
Name: Anna, Age: 30, Admin: True
</output>
```

### 2. Fixed data

```python
# Days of the week - a perfect example of an immutable sequence
DAYS_OF_WEEK = ("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday")

# Usage
today_index = 4  # Friday
print(f"Today is {DAYS_OF_WEEK[today_index]}")
<output>
Today is Friday
</output>
```

## Comparison of lists and tuples

Technically lists and tuples often overlap in what they can do, but in Python code they have **different roles**:

- **A tuple is a "record"**: each position has a specific meaning. `(name, age, email)` isn't "a list of three random things", it's a structure where the first slot is always the name, the second always the age, the third always the email.
- **A list is a homogeneous collection**: all elements have roughly the same "kind", and their count can grow or shrink as the program runs.

This distinction matters more than the technical details: even when both work, your choice affects readability and intent.

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
    <output>
    <class 'int'>
    </output>

    # Correct
    single_item_tuple = (42,)
    print(type(single_item_tuple))
    <output>
    <class 'tuple'>
    </output>
    ```

2. **Attempting to modify a tuple**

    ```python
    coordinates = (10.5, 20.7, 30.9)

    # This will raise an error
    try:
        coordinates[0] = 15.0
    except TypeError as e:
        print(f"Error: {e}")
    <output>
    Error: 'tuple' object does not support item assignment
    </output>
    ```

## Understanding check

**Which of the following statements about tuples in Python is true?**

1. **Correct answer:** Tuples can be used as dictionary keys — Tuples, unlike lists, are immutable, so they can be used as dictionary keys.

2. Tuples can be modified after creation — Tuples are immutable in Python. After creating a tuple, you cannot add, remove, or change its elements.

3. A tuple with a single element is written as (42) — A tuple with a single element must contain a comma after the element: (42,). Without the comma, (42) will be treated as just a number in parentheses.

4. Tuples can only contain elements of the same type — Tuples, like lists, can contain elements of different data types: numbers, strings, lists, other tuples, etc.

In the next lesson we'll look at [sets](https://python-academy.org/en/guide/sets) (`set`) — collections where every element is unique.
