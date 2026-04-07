# Iterations and Loops

Imagine that you need to repeat some action many times. For example, greeting each guest at a party or counting all items in a list. Doing this manually would be tedious. And this is where loops come to the rescue! 🔄

Loops help automate repetitive actions, allowing you to execute the same block of code multiple times. This saves time and makes your code cleaner and more efficient.

## What are iterations and loops?

> An iteration is a single execution of a set of instructions. And a loop is a construct that allows a block of code to be executed multiple times while a certain condition is met.

Python offers two main types of loops:

-   The `for` loop: used to iterate over a sequence (list, tuple, string, etc.)
-   The `while` loop: executes as long as the specified condition remains true

## The for loop

The `for` loop is used to iterate through elements of a sequence (list, tuple, string, etc.). This is the most common type of loop in Python.

### For loop syntax

The basic syntax of a for loop looks like this:

```python
# Simple example of a for loop
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(f"I love {fruit}!")
```

In this example:

1. `fruit` is the iterator variable that takes the value of each element in the `fruits` list in turn
2. The loop body executes for each element in the list
3. After `:` follows an indented code block that is executed in each iteration

### Iterating over a range of numbers

Often we need to execute a loop a certain number of times. For this, the `range()` function is used:

```python
# Using range() to create a sequence of numbers
for i in range(5):  # from 0 to 4
    print(f"Number: {i}")
```

The `range()` function creates a sequence of numbers and has several usage options:

```python
# Range() usage options

# range(stop): from 0 to stop-1
for i in range(3):
    print(i, end=" ")
print()  # For line break

# range(start, stop): from start to stop-1
for i in range(2, 5):
    print(i, end=" ")
print()

# range(start, stop, step): from start to stop-1 with step
for i in range(1, 10, 2):  # Odd numbers from 1 to 9
    print(i, end=" ")
```

### Iterating over strings

Strings in Python are also sequences, so we can iterate through their characters:

```python
# Iterating through characters in a string
message = "Python"

for char in message:
    print(char)
```

### Iterating with indices

Sometimes we need both elements and their indices. For this, the `enumerate()` function is used:

```python
# Getting indices and values
fruits = ["apple", "banana", "cherry"]

for index, fruit in enumerate(fruits):
    print(f"Index {index}: {fruit}")
```

## The while loop

The `while` loop executes as long as the specified condition remains true. This is useful when we don't know in advance how many times the loop should execute.

### While loop syntax

```python
# Simple example of a while loop
count = 1

while count <= 5:
    print(f"Loop is executing for the {count}th time")
    count += 1  # Increasing the counter
```

In this example:

1. The loop executes while `count <= 5`
2. Inside the loop, we increase `count` by 1 in each iteration
3. When `count` becomes 6, the condition becomes false, and the loop terminates

> **Important**: In a `while` loop, you always need to change the variable involved in the condition, otherwise the loop may become infinite!

### Infinite loop

If the condition in a `while` loop is always true, the loop will execute infinitely. This can be useful, but usually requires a way to exit such a loop:

```python
# Example with explicit breaking of an infinite loop
counter = 0

while True:  # This condition is always true
    print(f"Iteration {counter}")
    counter += 1

    if counter >= 5:  # Condition to exit the loop
        print("Exiting the loop!")
        break  # Interrupts the loop execution
```

## Loop control

Python provides several tools for controlling loop execution.

### The break statement

The `break` statement allows you to immediately exit a loop, regardless of the condition:

```python
# Using break to exit a loop
for i in range(10):
    print(i, end=" ")
    if i == 5:
        print("\nReached number 5, exiting the loop!")
        break
```

### The continue statement

The `continue` statement skips the current iteration and moves to the next one:

```python
# Using continue to skip iterations
for i in range(10):
    # Skip even numbers
    if i % 2 == 0:
        continue
    print(i, end=" ")
```

### The else block in loops

`for` and `while` loops can have an `else` block that executes when the loop terminates normally (not through `break`):

```python
# Using else with a loop
for i in range(5):
    print(i, end=" ")
else:
    print("\nLoop completed normally")

# Example with break - the else block won't execute
for i in range(5):
    print(i, end=" ")
    if i == 2:
        break
else:
    print("\nThis line won't be output because the loop was interrupted")
```

## Nested loops

Loops can be nested inside each other to process multidimensional data structures or solve more complex problems:

```python
# Example of nested loops - multiplication table
for i in range(1, 4):  # Rows
    for j in range(1, 4):  # Columns
        print(f"{i} × {j} = {i*j}", end="\t")
    print()  # Moving to a new line after each row of the table
```

> **Tip**: Nested loops can be resource-intensive, especially with a large number of iterations. Use them carefully and look for more efficient alternatives for processing large volumes of data.

## List Comprehensions

List comprehensions are an elegant way to create lists in one line using syntax similar to a `for` loop:

```python
# Creating a list of squares of numbers from 0 to 9
# Regular way
squares = []
for i in range(10):
    squares.append(i**2)
print(f"Regular way: {squares}")

# Using list comprehensions
squares_comprehension = [i**2 for i in range(10)]
print(f"Using list comprehension: {squares_comprehension}")
```

List comprehensions can also contain conditions:

```python
# Filtering with a condition: only even numbers
even_squares = [i**2 for i in range(10) if i % 2 == 0]
print(f"Squares of even numbers: {even_squares}")
```

## Understanding check

Let's check how well you've understood the topic of loops:

**What result will the following code give?**

```python
total = 0
for i in range(1, 5):
    if i % 2 == 0:
        total += i
print(total)
```
