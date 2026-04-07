# Iterations and Loops

> Iteration in Python is the process of sequentially traversing elements in a collection (such as a list, tuple, string).
> Loops are constructs that allow you to automate the iteration process and execute a block of code multiple times.

## The for Loop

The `for` loop in Python is used to iterate over sequences (lists, tuples, strings, and other iterable objects):

```python
# Iterating through a list
fruits = ["apple", "banana", "orange"]
for fruit in fruits:
    print(fruit)
# Output:
# apple
# banana
# orange

# Iterating through a string
for char in "Python":
    print(char)
# Output:
# P
# y
# t
# h
# o
# n
```

### The range() Function

The `range()` function generates a sequence of numbers and is often used with the `for` loop to execute code a certain number of times:

```python
# range(stop) - generates numbers from 0 to stop-1
for i in range(5):
    print(i)
# Output:
# 0
# 1
# 2
# 3
# 4

# range(start, stop) - generates numbers from start to stop-1
for i in range(2, 6):
    print(i)
# Output:
# 2
# 3
# 4
# 5

# range(start, stop, step) - generates numbers from start to stop-1 with step increment
for i in range(1, 10, 2):
    print(i)
# Output:
# 1
# 3
# 5
# 7
# 9
```

### The enumerate() Function

The `enumerate()` function allows you to get the index of an element along with the element itself:

```python
fruits = ["apple", "banana", "orange"]
for index, fruit in enumerate(fruits):
    print(f"Index: {index}, Value: {fruit}")
# Output:
# Index: 0, Value: apple
# Index: 1, Value: banana
# Index: 2, Value: orange

# You can set the starting index
for index, fruit in enumerate(fruits, 1):
    print(f"Number {index}: {fruit}")
# Output:
# Number 1: apple
# Number 2: banana
# Number 3: orange
```

### Iterating Over Multiple Sequences with zip()

The `zip()` function allows you to iterate over multiple sequences simultaneously:

```python
names = ["John", "Maria", "Alex"]
ages = [25, 30, 22]

for name, age in zip(names, ages):
    print(f"{name}: {age} years old")
# Output:
# John: 25 years old
# Maria: 30 years old
# Alex: 22 years old
```

## The while Loop

The `while` loop executes a block of code as long as a given condition remains true:

```python
# Basic while loop
count = 0
while count < 5:
    print(count)
    count += 1
# Output:
# 0
# 1
# 2
# 3
# 4

# Using a condition with user input
password = ""
while password != "secret":
    password = input("Enter the password: ")
print("Correct password!")
```

### Infinite Loops

If the condition in a `while` loop is always true, the loop will run infinitely. To exit such a loop, the `break` operator or exceptions are used:

```python
# Infinite loop with a conditional exit
while True:
    user_input = input("Enter 'exit' to quit: ")
    if user_input.lower() == "exit":
        break
    print(f"You entered: {user_input}")
```

## The break, continue, and else Operators in Loops

### The break Operator

The `break` operator allows you to exit a loop prematurely:

```python
# Searching in a list with exit upon finding
fruits = ["apple", "banana", "orange", "pear"]
for fruit in fruits:
    if fruit == "orange":
        print("Found an orange!")
        break
    print(f"Checking: {fruit}")
# Output:
# Checking: apple
# Checking: banana
# Found an orange!
```

### The continue Operator

The `continue` operator skips the remaining code in the current iteration and moves to the next one:

```python
# Skipping odd numbers
for i in range(1, 6):
    if i % 2 != 0:  # If the number is odd
        continue
    print(i)
# Output:
# 2
# 4
```

### The else Block in Loops

Both `for` and `while` can have an optional `else` block that executes if the loop completes normally (without a `break`):

```python
# Checking if a number is prime
num = 13
for i in range(2, num):
    if num % i == 0:
        print(f"{num} is not a prime number")
        break
else:
    print(f"{num} is a prime number")
# Output:
# 13 is a prime number

# Example with a while loop
count = 0
while count < 3:
    print(count)
    count += 1
else:
    print("The while loop completed normally")
# Output:
# 0
# 1
# 2
# The while loop completed normally
```

## List Comprehensions

List comprehensions are a compact way to create lists from other sequences:

```python
# Traditional way to create a list
squares = []
for i in range(1, 6):
    squares.append(i ** 2)
print(squares)  # [1, 4, 9, 16, 25]

# The same using a list comprehension
squares = [i ** 2 for i in range(1, 6)]
print(squares)  # [1, 4, 9, 16, 25]

# List comprehension with a condition
even_squares = [i ** 2 for i in range(1, 11) if i % 2 == 0]
print(even_squares)  # [4, 16, 36, 64, 100]

# List comprehension with multiple conditions (branching)
numbers = [i if i % 2 == 0 else -i for i in range(1, 6)]
print(numbers)  # [-1, 2, -3, 4, -5]
```

## Dictionary and Set Comprehensions

Similar to list comprehensions, you can create dictionary and set comprehensions:

```python
# Dictionary comprehension
word = "hello"
char_positions = {char: index for index, char in enumerate(word)}
print(char_positions)  # {'h': 0, 'e': 1, 'l': 3, 'o': 4}

# Set comprehension
vowels = "aeiou"
consonants = {char for char in "python" if char not in vowels}
print(consonants)  # {'p', 'y', 't', 'h', 'n'}
```

## Generator Expressions

Generator expressions are similar to list comprehensions but use parentheses instead of square brackets and generate values "on the fly," which saves memory:

```python
# List comprehension (creates the entire list in memory)
list_comp = [i ** 2 for i in range(1000000)]
# Takes up a lot of memory

# Generator expression (generates values on demand)
gen_exp = (i ** 2 for i in range(1000000))
# Takes minimal memory

# Using a generator expression with sum()
total = sum(i ** 2 for i in range(1, 6))
print(total)  # 55 (1 + 4 + 9 + 16 + 25)
```

## Iterating Over Dictionaries

There are several ways to iterate over dictionaries:

```python
person = {
    "name": "John",
    "age": 30,
    "city": "New York"
}

# Iterating over keys (default)
for key in person:
    print(key, person[key])

# Explicitly iterating over keys
for key in person.keys():
    print(key)

# Iterating over values
for value in person.values():
    print(value)

# Iterating over key-value pairs
for key, value in person.items():
    print(f"{key}: {value}")
```

## Nested Loops

Loops can be nested within each other:

```python
# Nested loops to create a multiplication table
for i in range(1, 4):
    for j in range(1, 4):
        print(f"{i} * {j} = {i * j}")
    print("-----")
# Output:
# 1 * 1 = 1
# 1 * 2 = 2
# 1 * 3 = 3
# -----
# 2 * 1 = 2
# 2 * 2 = 4
# 2 * 3 = 6
# -----
# 3 * 1 = 3
# 3 * 2 = 6
# 3 * 3 = 9
# -----
```

## Independent Exercises

1. Write a program that asks the user for numbers until they enter 0, and then outputs the sum of all entered numbers.
2. Create a list comprehension that generates a list of squares of even numbers from 1 to 20.
3. Write a program that prints all numbers from 1 to 100, but instead of numbers divisible by 3, it prints "Fizz", instead of numbers divisible by 5, it prints "Buzz", and instead of numbers divisible by both 3 and 5, it prints "FizzBuzz".
4. Using nested loops, create a program that prints the following pattern:
    ```
    *
    **
    ***
    ****
    *****
    ```
5. Create a dictionary where the keys are numbers from 1 to 10, and the values are their cubes, using a dictionary comprehension.
