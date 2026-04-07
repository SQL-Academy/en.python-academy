# Sets in Python

In this article, we'll look at sets in Python — collections of unique elements with special properties that make them indispensable for certain tasks.

## What is a set?

A set in Python is an unordered collection of unique elements. Two key properties of sets are:

1. **Unordered**: elements have no specific order and are not indexed
2. **Unique**: each element appears only once

Main characteristics of sets:

-   **Mutability**: you can add and remove elements
-   **Hashing**: elements must be hashable (immutable) objects
-   **Efficiency**: optimized for fast membership testing

Sets are based on the mathematical concept of sets, making them ideal for union, intersection, and difference operations.

## Creating sets

### Using curly braces {}

```python
# Set of integers
numbers = {1, 2, 3, 4, 5}
print(numbers)

# Automatic duplicate removal
duplicates = {1, 2, 2, 3, 3, 3, 4, 5, 5}
print(duplicates)

# Set with different data types
mixed = {1, "hello", (1, 2, 3)}
print(mixed)
```

> **Important**: You cannot create an empty set using `{}`, as this will create an empty dictionary. To create an empty set, use `set()`.

### Using the set() constructor

```python
# Empty set
empty_set = set()
print(empty_set)

# Creating a set from a list
numbers_set = set([1, 2, 2, 3, 4, 4, 5])
print(numbers_set)

# Creating a set from a string
letters = set("hello")
print(letters)  # 'l' appears only once
```

### Using set comprehensions

```python
# Set of squares of numbers from 0 to 9
squares = {x**2 for x in range(10)}
print(squares)
```

## Basic operations with sets

### Checking for element presence

```python
fruits = {"apple", "banana", "cherry"}

print("apple" in fruits)
print("pear" in fruits)
```

### Adding and removing elements

```python
fruits = {"apple", "banana"}

# Adding a single element
fruits.add("cherry")
print(fruits)

# Adding multiple elements
fruits.update(["pear", "orange"])
print(fruits)

# Removing an element
fruits.remove("banana")  # raises KeyError if element doesn't exist
print(fruits)

# Safely removing an element
fruits.discard("cherry")  # doesn't raise an error if element doesn't exist
print(fruits)

# Removing and returning an arbitrary element
random_fruit = fruits.pop()
print(random_fruit)
print(fruits)

# Clearing the set
fruits.clear()
print(fruits)
```

### Iterating through a set

```python
colors = {"red", "blue", "green"}

for color in colors:
    print(color)

# The order of elements is not guaranteed!
```

## Mathematical set operations

### Union

```python
a = {1, 2, 3}
b = {3, 4, 5}

# With the | operator
union_set = a | b
print(union_set)

# With the union() method
union_set = a.union(b)
print(union_set)
```

### Intersection

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

# With the & operator
intersection_set = a & b
print(intersection_set)

# With the intersection() method
intersection_set = a.intersection(b)
print(intersection_set)
```

### Difference

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

# With the - operator
difference_set = a - b
print(difference_set)

# With the difference() method
difference_set = a.difference(b)
print(difference_set)
```

## Comparing sets

```python
a = {1, 2, 3}
b = {1, 2, 3, 4, 5}
c = {1, 2, 3}

# Set equality
print(a == c)  # Contains the same elements

# Subsets
print(a.issubset(b))  # All elements of a are in b
print(a < b)  # a is a proper subset of b

# Supersets
print(b.issuperset(a))  # b contains all elements of a
print(b > a)  # b is a proper superset of a

# Checking for no common elements
d = {6, 7, 8}
print(a.isdisjoint(d))  # No common elements
```

## Immutable sets (frozenset)

If you need an immutable version of a set, use `frozenset`:

```python
# Creating a frozenset
immutable_set = frozenset([1, 2, 3, 4])
print(immutable_set)

# Attempting to modify a frozenset raises an error
try:
    immutable_set.add(5)
except AttributeError as e:
    print(f"Error: {e}")

# frozenset can be used as a dictionary key or an element of another set
normal_set = {frozenset([1, 2]), frozenset([3, 4])}
print(normal_set)
```

## Practical examples of using sets

### 1. Removing duplicates from a list

```python
numbers = [1, 2, 2, 3, 3, 3, 4, 5, 5]
unique_numbers = list(set(numbers))
print(unique_numbers)
```

### 2. Finding common elements

```python
users_group1 = ["Anna", "Ivan", "Maria", "Peter", "Elena"]
users_group2 = ["Ivan", "Olga", "Elena", "Alex"]

# Common elements (intersection)
common_users = set(users_group1) & set(users_group2)
print(f"Users in both groups: {common_users}")

# Elements only in the first group (difference)
only_group1 = set(users_group1) - set(users_group2)
print(f"Only in group 1: {only_group1}")

# All unique elements (union)
all_users = set(users_group1) | set(users_group2)
print(f"All unique users: {all_users}")
```

### 3. Checking for uniqueness of elements

```python
def are_all_unique(items):
    """Checks if all elements in a sequence are unique."""
    return len(set(items)) == len(items)

print(are_all_unique([1, 2, 3, 4, 5]))
print(are_all_unique([1, 2, 3, 3, 4]))
```

## Limitations and performance

### Limitations

Set elements must be hashable (immutable):

```python
# Works with immutable data types
valid_set = {1, "hello", (1, 2, 3)}
print(valid_set)

# Error with mutable data types
try:
    invalid_set = {1, [2, 3], {"a": 1}}
except TypeError as e:
    print(f"Error: {e}")
```

You can add:

-   Numbers (int, float, complex)
-   Strings (str)
-   Tuples (tuple) with hashable elements
-   Frozenset

You cannot add:

-   Lists (list)
-   Dictionaries (dict)
-   Sets (set)

### Performance

Sets are optimized for fast operations:

```python
import time

# Speed comparison (demonstration)
data = list(range(10000))
data_set = set(data)

# Search in list vs. search in set
start = time.time()
9999 in data  # Slow: O(n)
list_time = time.time() - start

start = time.time()
9999 in data_set  # Fast: O(1)
set_time = time.time() - start

print(f"Search in list: {list_time:.6f} sec")
print(f"Search in set: {set_time:.6f} sec")
print(f"Set is {list_time/set_time:.1f} times faster")
```

Operations with O(1) complexity (constant time):

-   Testing for membership: `x in set`
-   Adding an element: `set.add(x)`
-   Removing an element: `set.remove(x)`, `set.discard(x)`
