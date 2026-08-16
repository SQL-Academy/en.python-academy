---
meta:
    title: "Sets in Python"
    description: "Detailed description of sets in Python: creation, methods for working with sets, set operations, and practical usage examples."
---

# Sets in Python

A set (`set`) in Python solves two specific tasks: fast membership testing (is an element in the collection?) and storing **only unique** values without duplicates. It's an unordered collection based on the mathematical concept of a set.

## What is a set?

A set in Python is an unordered collection of unique elements. Two key properties of sets:

1. **Unordered**: elements have no specific order and are not indexed — you can't reach for "the third element of a set", only iterate over all of them or check whether a particular one is present
2. **Unique**: each element appears only once

Main characteristics of sets:

- **Mutability**: you can add and remove elements
- **Immutable elements**: only immutable objects can go inside a set (numbers, strings, tuples)
- **Efficiency**: optimized for fast membership testing

Because sets are based on the mathematical concept, they support union, intersection, and difference operations.

## Creating sets

### Using curly braces {}

```python
# Set of integers
numbers = {1, 2, 3, 4, 5}
print(numbers)
<output>
{1, 2, 3, 4, 5}
</output>

# Automatic duplicate removal
duplicates = {1, 2, 2, 3, 3, 3, 4, 5, 5}
print(duplicates)
<output>
{1, 2, 3, 4, 5}
</output>

# A single set can hold different immutable types
mixed = {1, "hello", (1, 2, 3)}
print(len(mixed))  # number, string and tuple — all three fit
<output>
3
</output>
```

> **Important**: You cannot create an empty set using `{}`, as this will create an empty dictionary. To create an empty set, use `set()`.

### Using the set() constructor

```python
# Empty set
empty_set = set()
print(empty_set)
<output>
set()
</output>

# Creating a set from a list
numbers_set = set([1, 2, 2, 3, 4, 4, 5])
print(numbers_set)
<output>
{1, 2, 3, 4, 5}
</output>

# Creating a set from a string — repeated letters collapse
letters = set("hello")
print(len(letters))  # "hello" has two 'l', the set keeps one — 4 letters total
<output>
4
</output>
```

## Basic operations with sets

### Checking for element presence

```python
fruits = {"apple", "banana", "cherry"}

print("apple" in fruits)
<output>
True
</output>
print("pear" in fruits)
<output>
False
</output>
```

### Adding and removing elements

The order of elements in a set is arbitrary, so in the string examples below we print them via `sorted()`, which returns a sorted list — that keeps the output from jumping around between runs.

```python
fruits = {"apple", "banana"}

# Adding a single element
fruits.add("cherry")
print(sorted(fruits))
<output>
['apple', 'banana', 'cherry']
</output>

# Adding multiple elements
fruits.update(["pear", "orange"])
print(sorted(fruits))
<output>
['apple', 'banana', 'cherry', 'orange', 'pear']
</output>

# Removing an element
fruits.remove("banana")  # raises KeyError if element doesn't exist
print(sorted(fruits))
<output>
['apple', 'cherry', 'orange', 'pear']
</output>

# Safely removing an element
fruits.discard("cherry")  # doesn't raise an error if element doesn't exist
print(sorted(fruits))
<output>
['apple', 'orange', 'pear']
</output>

# pop() removes and returns some element — which one exactly is not known in advance
removed = fruits.pop()
print(len(fruits))  # one fewer than before
<output>
2
</output>

# Clearing the set
fruits.clear()
print(fruits)
<output>
set()
</output>
```

### Looping over a set

You go through a set with a `for` loop. The order is arbitrary and may change from run to run — that's what "unordered" means. When you need a predictable order, sort with `sorted()`:

```python
colors = {"red", "blue", "green"}

for color in sorted(colors):
    print(color)
<output>
blue
green
red
</output>
```

## Mathematical set operations

Three main operations: union, intersection, and difference. They're easy to visualise with Venn diagrams:

![Venn diagrams for the three set operations: union A | B, intersection A & B, and difference A - B](https://python-academy.org/static/guidePage/sets/set-operations-en.webp "Set operations")

### Union

All elements from both sets:

```python
a = {1, 2, 3}
b = {3, 4, 5}

union_set = a | b
print(union_set)
<output>
{1, 2, 3, 4, 5}
</output>
```

The same can be written as `a.union(b)`.

### Intersection

Elements that are in both sets:

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

intersection_set = a & b
print(intersection_set)
<output>
{3, 4}
</output>
```

The same can be written as `a.intersection(b)`.

### Difference

Elements from the first set that are not in the second:

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

difference_set = a - b
print(difference_set)
<output>
{1, 2}
</output>
```

The same can be written as `a.difference(b)`.

## Comparing sets

```python
a = {1, 2, 3}
b = {1, 2, 3, 4, 5}
c = {1, 2, 3}

# Set equality
print(a == c)  # Contains the same elements
<output>
True
</output>

# Subsets
print(a.issubset(b))  # All elements of a are in b
<output>
True
</output>
print(a < b)  # a is a proper subset of b
<output>
True
</output>

# Supersets
print(b.issuperset(a))  # b contains all elements of a
<output>
True
</output>
print(b > a)  # b is a proper superset of a
<output>
True
</output>

# Checking for no common elements
d = {6, 7, 8}
print(a.isdisjoint(d))  # No common elements
<output>
True
</output>
```

## Immutable sets (frozenset)

If you need an immutable version of a set, use `frozenset`:

```python
# Creating a frozenset
immutable_set = frozenset([1, 2, 3, 4])
print(immutable_set)
<output>
frozenset({1, 2, 3, 4})
</output>

# Attempting to modify a frozenset raises an error
try:
    immutable_set.add(5)
except AttributeError as e:
    print(f"Error: {e}")
<output>
Error: 'frozenset' object has no attribute 'add'
</output>

# frozenset can be used as a dictionary key or an element of another set
normal_set = {frozenset([1, 2]), frozenset([3, 4])}
print(len(normal_set))  # both frozensets fit inside
<output>
2
</output>
```

## Practical examples of using sets

### 1. Removing duplicates from a list

```python
numbers = [1, 2, 2, 3, 3, 3, 4, 5, 5]
unique_numbers = list(set(numbers))
print(unique_numbers)
<output>
[1, 2, 3, 4, 5]
</output>
```

### 2. Finding common elements

```python
users_group1 = ["Anna", "Ivan", "Maria", "Peter", "Elena"]
users_group2 = ["Ivan", "Olga", "Elena", "Alex"]

# Common elements (intersection)
common_users = set(users_group1) & set(users_group2)
print(f"Users in both groups: {sorted(common_users)}")
<output>
Users in both groups: ['Elena', 'Ivan']
</output>

# Elements only in the first group (difference)
only_group1 = set(users_group1) - set(users_group2)
print(f"Only in group 1: {sorted(only_group1)}")
<output>
Only in group 1: ['Anna', 'Maria', 'Peter']
</output>

# All unique elements (union)
all_users = set(users_group1) | set(users_group2)
print(f"All unique users: {sorted(all_users)}")
<output>
All unique users: ['Alex', 'Anna', 'Elena', 'Ivan', 'Maria', 'Olga', 'Peter']
</output>
```

### 3. Checking for uniqueness of elements

```python
def are_all_unique(items):
    """Checks if all elements in a sequence are unique."""
    return len(set(items)) == len(items)

print(are_all_unique([1, 2, 3, 4, 5]))
<output>
True
</output>
print(are_all_unique([1, 2, 3, 3, 4]))
<output>
False
</output>
```

## Limitations and performance

### Limitations

Set elements must be hashable (immutable):

```python
# Works with immutable data types
valid_set = {1, "hello", (1, 2, 3)}
print(len(valid_set))  # number, string and tuple are all hashable — all three fit
<output>
3
</output>

# Error with mutable data types
try:
    invalid_set = {1, [2, 3], {"a": 1}}
except TypeError as e:
    print(f"Error: {e}")
<output>
Error: unhashable type: 'list'
</output>
```

You can add:

- Numbers (int, float, complex)
- Strings (str)
- Tuples (tuple) with hashable elements
- Frozenset

You cannot add:

- Lists (list)
- Dictionaries (dict)
- Sets (set)

### Performance

Fast lookup is exactly what sets are built for. Let's test on a million numbers: we look for the last one — the worst case for a list, which has to scan through everything.

```python
import time

data = list(range(1_000_000))
data_set = set(data)

start = time.time()
for _ in range(100):
    999_999 in data
list_time = time.time() - start

start = time.time()
for _ in range(100):
    999_999 in data_set
set_time = time.time() - start

print(f"Search in list: {list_time:.3f} sec")
<output>
Search in list: 0.442 sec
</output>
print(f"Search in set: {set_time:.5f} sec")
<output>
Search in set: 0.00001 sec
</output>
```

Your exact numbers will differ — they depend on the machine and how busy it is — but the gap stays just as wide: tens of thousands of times. The list has to check elements one by one until it finds the right one. Instead of scanning, a set computes straight away where the value should sit and checks only that spot — and it does so equally fast whether there are ten elements or a million.

Operations with O(1) complexity (constant time):

- Testing for membership: `x in set`
- Adding an element: `set.add(x)`
- Removing an element: `set.remove(x)`, `set.discard(x)`

## Check your understanding

**What does `print(set([1, 2, 2, 3, 3, 3]))` output?**

1. \{1, 2, 2, 3, 3, 3} — A set does not keep duplicates. Repeated values collapse, and each number stays in a single copy.

2. **Correct answer:** \{1, 2, 3} — A set keeps only unique values, so the repeated 2s and 3s are dropped. This is exactly why set() is a common way to remove duplicates from a list.

3. \[1, 2, 3] — The uniqueness is right, but the type is not: set() returns a set in curly braces, not a list. To get a list, wrap the result in list().
