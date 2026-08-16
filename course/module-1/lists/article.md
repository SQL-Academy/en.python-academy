---
meta:
    title: "Lists in Python"
    description: "Detailed description of lists in Python: creation, methods for working with lists, operations, and practical usage examples."
---

# Lists in Python

Say you're keeping a shopping list. You could make a separate variable for each item: `item1`, `item2`, `item3`. But that turns into a mess fast: you can't insert an item in the middle, you can't count how many items there are, and you can't go through them all in turn. You need one variable that holds many values in order. That's a list.

```python
shopping = ["bread", "milk", "eggs"]
print(shopping)
<output>
['bread', 'milk', 'eggs']
</output>
```

> A list (list) is an ordered, mutable collection of elements. Under a single name lie several values at once, each has its own position, and the set can be changed: add, remove, rearrange.

From this definition follow four properties we'll rely on going forward:

- **Order is preserved** — elements stay in the order you added them
- **A list is mutable** — after creation you can add and remove elements
- **Access by index** — any element can be reached by its position
- **Duplicates are allowed** — the same value can appear multiple times

## Creating lists

There are several ways to create lists in Python:

### Using square brackets \[]

```python
# Empty list
empty_list = []
print(empty_list)
<output>
[]
</output>

# List of numbers
numbers = [1, 2, 3, 4, 5]
print(numbers)
<output>
[1, 2, 3, 4, 5]
</output>

# List of different data types
mixed = [1, "hello", True, 3.14]
print(mixed)
<output>
[1, 'hello', True, 3.14]
</output>

# Nested lists (list of lists)
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
print(matrix)
<output>
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]
</output>
```

### Using the list() constructor

```python
# Creating an empty list
empty_list = list()
print(empty_list)
<output>
[]
</output>

# Creating a list from a string (each character becomes an element)
chars = list("Python")
print(chars)
<output>
['P', 'y', 't', 'h', 'o', 'n']
</output>

# Creating a list from other iterable objects
tuple_to_list = list((1, 2, 3))
print(tuple_to_list)
<output>
[1, 2, 3]
</output>
```

## Accessing list elements

### Indexing

In Python, indexing starts at 0, meaning the first element has index 0, the second one has index 1, and so on:

```python
fruits = ["apple", "banana", "cherry", "pear", "orange"]

# Getting elements by index
first_fruit = fruits[0]
print(f"First fruit: {first_fruit}")
<output>
First fruit: apple
</output>

# Negative indices for accessing from the end of the list
last_fruit = fruits[-1]
print(f"Last fruit: {last_fruit}")
<output>
Last fruit: orange
</output>

second_last = fruits[-2]
print(f"Second to last fruit: {second_last}")
<output>
Second to last fruit: pear
</output>
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
<output>
First three fruits: ['apple', 'banana', 'cherry']
</output>

# Same thing, but the starting index can be omitted if it's 0
first_three = fruits[:3]
print(f"First three fruits: {first_three}")
<output>
First three fruits: ['apple', 'banana', 'cherry']
</output>

# Every second element
every_second = fruits[::2]
print(f"Every second fruit: {every_second}")
<output>
Every second fruit: ['apple', 'cherry', 'orange']
</output>

# Reversing the list
reversed_list = fruits[::-1]
print(f"List in reverse order: {reversed_list}")
<output>
List in reverse order: ['orange', 'pear', 'cherry', 'banana', 'apple']
</output>
```

## Looping over a list

Reaching for elements by index is handy when you need one or two. But to go through them all, there's a shorter way — the `for` loop you already know:

```python
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(f"Fruit: {fruit}")
<output>
Fruit: apple
Fruit: banana
Fruit: cherry
</output>
```

To ask how many elements a list holds, use the `len()` function:

```python
fruits = ["apple", "banana", "cherry"]
print(f"Fruits in total: {len(fruits)}")
<output>
Fruits in total: 3
</output>
```

Sometimes you need the position along with the element. No need for a separate counter variable — `enumerate()` hands you the index and the value as a pair:

```python
fruits = ["apple", "banana", "cherry"]

for index, fruit in enumerate(fruits):
    print(f"Index {index}: {fruit}")
<output>
Index 0: apple
Index 1: banana
Index 2: cherry
</output>
```

## Modifying list elements

The key difference from strings: **lists are mutable**. A string can't be changed in place — you have to build a new one (recall the strings lesson). But list elements can be changed, added, and removed inside the existing object:

```python
fruits = ["apple", "banana", "cherry"]

# Changing an element
fruits[0] = "kiwi"
print(fruits)
<output>
['kiwi', 'banana', 'cherry']
</output>

# Changing multiple elements using a slice
numbers = [1, 2, 3, 4, 5]
numbers[1:4] = [20, 30, 40]
print(numbers)
<output>
[1, 20, 30, 40, 5]
</output>

# You can even replace multiple elements with a different number of elements
numbers = [1, 2, 3, 4, 5]
numbers[1:4] = [20, 30]
print(numbers)
<output>
[1, 20, 30, 5]
</output>
```

## Main list methods

Python provides many built-in methods for working with lists:

### Adding elements

```python
fruits = ["apple", "banana"]

# Adding an element to the end of the list
fruits.append("cherry")
print(fruits)
<output>
['apple', 'banana', 'cherry']
</output>

# Inserting an element at a specific position
fruits.insert(1, "orange")
print(fruits)
<output>
['apple', 'orange', 'banana', 'cherry']
</output>

# Adding elements from another list
more_fruits = ["pear", "grape"]
fruits.extend(more_fruits)
print(fruits)
<output>
['apple', 'orange', 'banana', 'cherry', 'pear', 'grape']
</output>

# Combining lists using the + operator
combined = fruits + ["pineapple", "mango"]
print(combined)
<output>
['apple', 'orange', 'banana', 'cherry', 'pear', 'grape', 'pineapple', 'mango']
</output>
```

### Removing elements

```python
fruits = ["apple", "banana", "cherry", "orange", "banana"]

# Removing an element by value (removes only the first occurrence)
fruits.remove("banana")
print(fruits)
<output>
['apple', 'cherry', 'orange', 'banana']
</output>

# Removing an element by index and returning its value
removed = fruits.pop(1)
print(f"Removed: {removed}")
<output>
Removed: cherry
</output>
print(f"List after removal: {fruits}")
<output>
List after removal: ['apple', 'orange', 'banana']
</output>

# If no index is specified, pop() removes and returns the last element
last = fruits.pop()
print(f"Last element: {last}")
<output>
Last element: banana
</output>
print(fruits)
<output>
['apple', 'orange']
</output>

# Removing all elements from the list
fruits.clear()
print(f"Empty list: {fruits}")
<output>
Empty list: []
</output>

# The del operator for removing elements by index or slice
numbers = [1, 2, 3, 4, 5]
del numbers[0]
print(numbers)
<output>
[2, 3, 4, 5]
</output>

del numbers[1:3]
print(numbers)
<output>
[2, 5]
</output>
```

### Finding and counting elements

```python
fruits = ["apple", "banana", "cherry", "banana", "pear"]

# Checking if an element is in the list
print("banana" in fruits)
<output>
True
</output>
print("watermelon" in fruits)
<output>
False
</output>

# Finding the index of the first occurrence of an element
banana_index = fruits.index("banana")
print(f"Index of the first banana: {banana_index}")
<output>
Index of the first banana: 1
</output>

# Counting the number of occurrences of an element
banana_count = fruits.count("banana")
print(f"Number of bananas: {banana_count}")
<output>
Number of bananas: 2
</output>
```

### Sorting and reversing

```python
# Sorting a list (modifies the original list)
numbers = [3, 1, 4, 1, 5, 9, 2]
numbers.sort()
print(f"Sorted list: {numbers}")
<output>
Sorted list: [1, 1, 2, 3, 4, 5, 9]
</output>

# Sorting in reverse order
numbers.sort(reverse=True)
print(f"Reverse sort: {numbers}")
<output>
Reverse sort: [9, 5, 4, 3, 2, 1, 1]
</output>

# If you don't want to modify the original list, use sorted()
original = [3, 1, 4, 1, 5]
sorted_list = sorted(original)
print(f"Original: {original}")
<output>
Original: [3, 1, 4, 1, 5]
</output>
print(f"Sorted copy: {sorted_list}")
<output>
Sorted copy: [1, 1, 3, 4, 5]
</output>
```

## Copying lists

This is one of the most common sources of bugs for beginners: assigning a list **does not create a copy**. Both variables point to the same object in memory. If you change the list through one variable, the other will see the change.

![Assignment vs copying: with reference = original both variables point to the same box, while copy = original.copy() creates a new box](https://python-academy.org/static/guidePage/lists/copy-vs-reference-en.webp "Assignment vs copying")

```python
# Create a list
original = [1, 2, 3]

# Assignment doesn't create a copy — both variables point to the same list
reference = original
reference.append(4)
print(f"Original after changing the reference: {original}")
<output>
Original after changing the reference: [1, 2, 3, 4]
</output>

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
<output>
Original: [1, 2, 3, 4]
</output>
print(f"Copy 1: {copy1}")
<output>
Copy 1: [1, 2, 3, 4, 5]
</output>
```

## List comprehensions

A very common task: take one list and build another from it. Done plainly, that's a few lines — set up an empty list, walk it with a loop, append one by one:

```python
squares = []
for i in range(10):
    squares.append(i**2)
print(squares)
<output>
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
</output>
```

Common enough that Python has a shorthand for it — the **list comprehension**. Same result, one line:

```python
squares = [i**2 for i in range(10)]
print(squares)
<output>
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
</output>
```

Read it not left to right but starting from the `for`: `for i in range(10)` is where the values come from, `i**2` is what goes into the new list. You can add a condition at the end, and then only the matching values make it through:

```python
even_squares = [i**2 for i in range(10) if i % 2 == 0]
print(even_squares)
<output>
[0, 4, 16, 36, 64]
</output>
```

The temptation to cram everything into one line is strong, but a comprehension is good exactly as long as it reads at a glance. Once two conditions and a nested loop want in, a plain `for` will be clearer — both for you and for whoever opens the code six months from now.

## Practical example of using a list

```python
# List of students and their grades
students = ["Anna", "John", "Maria", "Peter", "Elena"]
grades = [95, 82, 90, 78, 88]

# Finding the student with the highest score
highest_score = max(grades)
top_student_index = grades.index(highest_score)
print(f"Best student: {students[top_student_index]} with a grade of {highest_score}")
<output>
Best student: Anna with a grade of 95
</output>
```

## Understanding check

**Which of the following methods will correctly create a copy of a list that is not linked to the original?**

1. copy_list = original_list — This does not create a copy, but merely gives another name to the same list. Changes in copy_list will be reflected in original_list.

2. **Correct answer:** copy_list = original_list.copy() — The .copy() method creates a new copy of the list. Changes to the copy will not affect the original (however, be careful with nested objects - this is a shallow copy).

3. **Correct answer:** copy_list = original_list\[:] — Using the slice \[:] creates a new copy of the entire list. This is one of the correct ways to create a copy in Python.

4. **Correct answer:** copy_list = list(original_list) — The list() function creates a new list based on an existing one, which is a proper way to create a copy.
