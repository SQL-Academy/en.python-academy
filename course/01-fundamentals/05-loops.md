---
meta:
    title: "Iterations and Loops"
    description: "Loops in Python: for and while, iterating over strings and number ranges, break and continue, nested loops and practical examples."
---

# Iterations and Loops

You need to print a word letter by letter — each on its own line. Without a loop that's six separate `print` calls: one for "P", one for "y", one for "t", and so on. A longer word means more lines by hand, and if the word only becomes known while the program runs, you can't write them out in advance.

A loop spares you that: you describe the step once, and Python repeats it for each letter on its own. One such repetition is called an **iteration**.

Python has two loops: `for` walks through a ready-made set of values, `while` keeps going as long as a condition holds. Let's start with the first.

## The for loop

A string is a sequence of characters, and `for` can walk straight through it:

```python
message = "Python"

for char in message:
    print(char)
<output>
P
y
t
h
o
n
</output>
```

It reads almost like English: "for each character `char` in the string `message` — print it". Python takes the values one by one, binds each to the name `char`, and runs the indented body. When the values run out, the loop ends — you don't have to track that yourself.

`for` walks any set of values the same way, not just strings: lists, tuples and dictionaries are waiting in the chapters ahead, and you'll walk them with this very loop.

### Iterating over a range of numbers

Often we need to execute a loop a certain number of times. For this, the `range()` function is used:

```python
# Using range() to create a sequence of numbers
for i in range(5):  # from 0 to 4
    print(f"Number: {i}")
<output>
Number: 0
Number: 1
Number: 2
Number: 3
Number: 4
</output>
```

The `range()` function creates a sequence of numbers and has several usage options.

The examples below use `print(..., end=" ")`: by default `print` appends a newline at the end, but the `end=" "` parameter replaces it with a space so values print on a single line.

```python
# Range() usage options

# range(stop): from 0 to stop-1
for i in range(3):
    print(i, end=" ")
print()  # For line break
<output>
0 1 2
</output>

# range(start, stop): from start to stop-1
for i in range(2, 5):
    print(i, end=" ")
print()
<output>
2 3 4
</output>

# range(start, stop, step): from start to stop-1 with step
for i in range(1, 10, 2):  # Odd numbers from 1 to 9
    print(i, end=" ")
<output>
1 3 5 7 9
</output>
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
<output>
Loop is executing for the 1th time
Loop is executing for the 2th time
Loop is executing for the 3th time
Loop is executing for the 4th time
Loop is executing for the 5th time
</output>
```

The loop runs while the condition `count <= 5` is true. Inside, you must change the variable from the condition (here `count += 1`), otherwise the loop will be infinite.

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
<output>
Iteration 0
Iteration 1
Iteration 2
Iteration 3
Iteration 4
Exiting the loop!
</output>
```

## Loop control

Python provides several tools for controlling loop execution. The two main ones are `break` and `continue`.

### The break statement

The `break` statement allows you to immediately exit a loop, regardless of the condition:

![The loop walks through numbers 0–9, hits break at 5 and exits the loop; numbers 6–9 are not visited](https://python-academy.org/static/guidePage/loops/break-en.webp "break — exit the loop")

```python
# Using break to exit a loop
for i in range(10):
    print(i, end=" ")
    if i == 5:
        print("\nReached number 5, exiting the loop!")
        break
<output>
0 1 2 3 4 5
Reached number 5, exiting the loop!
</output>
```

### The continue statement

The `continue` statement skips the current iteration and moves to the next one:

![The loop walks through numbers 0–5, hits continue at 3, skips over it, and resumes at 4](https://python-academy.org/static/guidePage/loops/continue-en.webp "continue — skip iteration")

```python
# Using continue to skip iterations
for i in range(10):
    # Skip even numbers
    if i % 2 == 0:
        continue
    print(i, end=" ")
<output>
1 3 5 7 9
</output>
```

### The else block in loops

`for` and `while` loops can have an `else` block that runs when the loop ends normally (not via `break`):

```python
for i in range(5):
    print(i, end=" ")
else:
    print("\nLoop completed without break")
<output>
0 1 2 3 4
Loop completed without break
</output>
```

The feature exists, but it's rarely used in real-world code.

## Nested loops

Loops can be nested inside each other to process multidimensional data structures or solve more complex problems:

```python
# Example of nested loops - multiplication table
for i in range(1, 4):  # Rows
    for j in range(1, 4):  # Columns
        print(f"{i} × {j} = {i*j}", end="\t")
    print()  # Moving to a new line after each row of the table
<output>
1 × 1 = 1	1 × 2 = 2	1 × 3 = 3
2 × 1 = 2	2 × 2 = 4	2 × 3 = 6
3 × 1 = 3	3 × 2 = 6	3 × 3 = 9
</output>
```

> **Tip**: Nested loops can be resource-intensive, especially with a large number of iterations. Use them carefully and look for more efficient alternatives for processing large volumes of data.

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

1. 0 — In the loop we sum all even numbers from 1 to 4, which are 2 and 4.

2. **Correct answer:** 6 — We sum the even numbers from 1 to 4, which are 2 + 4 = 6.

3. 10 — The number 10 would be the result of summing all numbers from 1 to 4 (1 + 2 + 3 + 4), but in this code only even numbers are summed.

4. 3 — The number 3 would be the result of summing 1 and 2, but in this code only even numbers from 1 to 4 are summed, namely 2 and 4.
