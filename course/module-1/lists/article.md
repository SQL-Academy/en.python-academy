# Lists in Python

In this article, we will take a detailed look at lists in Python — one of the most flexible and frequently used data types. Lists allow you to store collections of elements and manipulate them in various ways.

## What is a list?

A list in Python is an ordered, mutable collection of elements that can be of different types. You can think of a list as a container that stores various objects arranged in a specific order.

The main properties of lists:

-   **Ordered**: elements are stored in the order they were added
-   **Mutable**: you can add, remove, and change elements after creating the list
-   **Indexable**: each element can be accessed by its position (index)
-   **Allow duplicates**: the same element can appear in the list multiple times

## Creating lists

There are several ways to create lists in Python:

### Using square brackets []

```python
# Empty list
empty_list = []
print(empty_list)

# List of numbers
numbers = [1, 2, 3, 4, 5]
print(numbers)

# List of different data types
mixed = [1, "hello", True, 3.14]
print(mixed)

# Nested lists (list of lists)
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
print(matrix)
```

### Using the list() constructor

```python
# Creating an empty list
empty_list = list()
print(empty_list)

# Creating a list from a string (each character becomes an element)
chars = list("Python")
print(chars)

# Creating a list from other iterable objects
tuple_to_list = list((1, 2, 3))
print(tuple_to_list)

set_to_list = list({1, 2, 3})
print(set_to_list)
```

### Using list comprehensions

```python
# Creating a list of squares of numbers from 0 to 9
squares = [x**2 for x in range(10)]
print(squares)

# Creating a list of even numbers from 0 to 9
even_numbers = [x for x in range(10) if x % 2 == 0]
print(even_numbers)
```

## Accessing list elements

### Indexing

In Python, indexing starts at 0, meaning the first element has index 0, the second one has index 1, and so on:

```python
fruits = ["apple", "banana", "cherry", "pear", "orange"]

# Getting elements by index
first_fruit = fruits[0]
print(f"First fruit: {first_fruit}")

# Negative indices for accessing from the end of the list
last_fruit = fruits[-1]
print(f"Last fruit: {last_fruit}")

second_last = fruits[-2]
print(f"Second to last fruit: {second_last}")
```

### Slices

Slices allow you to get a sublist by specifying start and end indices:

```python
fruits = ["apple", "banana", "cherry", "pear", "orange"]

# Slice syntax: list[start:end:step]
# Start is included, end is not included!

# First three elements
first_three = fruits[0:3]
print(f"First three fruits: {first_three}")

# Same thing, but the starting index can be omitted if it's 0
first_three = fruits[:3]
print(f"First three fruits: {first_three}")

# Every second element
every_second = fruits[::2]
print(f"Every second fruit: {every_second}")

# Reversing the list
reversed_list = fruits[::-1]
print(f"List in reverse order: {reversed_list}")
```

## Modifying list elements

Unlike strings, lists are mutable, meaning you can change, add, and remove elements:

```python
fruits = ["apple", "banana", "cherry"]

# Changing an element
fruits[0] = "kiwi"
print(fruits)

# Changing multiple elements using a slice
numbers = [1, 2, 3, 4, 5]
numbers[1:4] = [20, 30, 40]
print(numbers)

# You can even replace multiple elements with a different number of elements
numbers = [1, 2, 3, 4, 5]
numbers[1:4] = [20, 30]
print(numbers)
```

## Main list methods

Python provides many built-in methods for working with lists:

### Adding elements

```python
fruits = ["apple", "banana"]

# Adding an element to the end of the list
fruits.append("cherry")
print(fruits)

# Inserting an element at a specific position
fruits.insert(1, "orange")
print(fruits)

# Adding elements from another list
more_fruits = ["pear", "grape"]
fruits.extend(more_fruits)
print(fruits)

# Combining lists using the + operator
combined = fruits + ["pineapple", "mango"]
print(combined)
```

### Removing elements

```python
fruits = ["apple", "banana", "cherry", "orange", "banana"]

# Removing an element by value (removes only the first occurrence)
fruits.remove("banana")
print(fruits)

# Removing an element by index and returning its value
removed = fruits.pop(1)
print(f"Removed: {removed}")
print(f"List after removal: {fruits}")

# If no index is specified, pop() removes and returns the last element
last = fruits.pop()
print(f"Last element: {last}")
print(fruits)

# Removing all elements from the list
fruits.clear()
print(f"Empty list: {fruits}")

# The del operator for removing elements by index or slice
numbers = [1, 2, 3, 4, 5]
del numbers[0]
print(numbers)

del numbers[1:3]
print(numbers)
```

### Finding and counting elements

```python
fruits = ["apple", "banana", "cherry", "banana", "pear"]

# Checking if an element is in the list
print("banana" in fruits)
print("watermelon" in fruits)

# Finding the index of the first occurrence of an element
banana_index = fruits.index("banana")
print(f"Index of the first banana: {banana_index}")

# Counting the number of occurrences of an element
banana_count = fruits.count("banana")
print(f"Number of bananas: {banana_count}")
```

### Sorting and reversing

```python
# Sorting a list (modifies the original list)
numbers = [3, 1, 4, 1, 5, 9, 2]
numbers.sort()
print(f"Sorted list: {numbers}")

# Sorting in reverse order
numbers.sort(reverse=True)
print(f"Reverse sort: {numbers}")

# If you don't want to modify the original list, use sorted()
original = [3, 1, 4, 1, 5]
sorted_list = sorted(original)
print(f"Original: {original}")
print(f"Sorted copy: {sorted_list}")
```

## Copying lists

When working with lists, it's important to understand how assignment and copying work:

```python
# Create a list
original = [1, 2, 3]

# Assignment doesn't create a copy — both variables point to the same list
reference = original
reference.append(4)
print(f"Original after changing the reference: {original}")

# Correct ways to copy a list:
# 1. The copy() method
copy1 = original.copy()

# 2. Using slice [:]
copy2 = original[:]

# 3. The list() function
copy3 = list(original)

# Let's check that the copies are not linked to the original
copy1.append(5)
print(f"Original: {original}")
print(f"Copy 1: {copy1}")
```

## Practical example of using a list

```python
# List of students and their grades
students = ["Anna", "John", "Maria", "Peter", "Elena"]
grades = [95, 82, 90, 78, 88]

# Finding the student with the highest score
highest_score = max(grades)
top_student_index = grades.index(highest_score)
print(f"Best student: {students[top_student_index]} with a grade of {highest_score}")
```

## Understanding check

**Which of the following methods will correctly create a copy of a list that is not linked to the original?**
