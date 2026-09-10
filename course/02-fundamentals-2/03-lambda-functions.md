---
meta:
    title: "Lambda Functions"
    description: "Tiny nameless functions: the lambda syntax and its use with sorted, map and filter."
---

# Lambda Functions in Python

A list of numbers `sorted()` handles on its own: with numbers everything is clear — smaller, larger. But a list of students raises a question: what should be compared — names, grades? Python doesn't know.

To give it a hint, `sorted()` accepts a small helper function: it takes one student and returns the value to compare by — the grade, for example. Defining a full function with `def` for one line like that, naming it, putting it in its own block — clunky.

For cases like these there are lambda functions: a tiny function right where it's needed, with no name and no `def`.

> A lambda function is a nameless function of a single expression: it takes arguments and returns the result of that expression. It follows this formula:

```python
lambda arguments: expression
```

## A lambda next to a regular function

Let's compare a regular function and a lambda that do the same thing:

```python
def square(x):
    return x * x

square_lambda = lambda x: x * x

print(square(5))
<output>
25
</output>
print(square_lambda(5))
<output>
25
</output>
```

The notation is shorter: no name, no `return` — the result of the single expression is returned. That said, you'll rarely store a lambda in a variable like this: its strength is being passed directly to wherever a small function is needed. Let's start with the task from the introduction.

## sorted(): sorting by your own rule

`sorted()` has a `key` parameter — a function that pulls out of each element the value to compare by. The lambda goes right into the call:

```python
students = [
    {"name": "Alice", "grade": 85},
    {"name": "Bob", "grade": 92},
    {"name": "Charlie", "grade": 78},
]

sorted_by_grade = sorted(students, key=lambda student: student["grade"], reverse=True)
for student in sorted_by_grade:
    print(f"{student['name']}: {student['grade']}")
<output>
Bob: 92
Alice: 85
Charlie: 78
</output>
```

`key=lambda student: student["grade"]` is that very helper function from the introduction: it takes a student and returns their grade. And `reverse=True` flips the order from highest to lowest. The comparison rule can be anything — for example, let's sort numbers by absolute value:

```python
numbers = [5, -3, 2, -8, 1, 0, -2]

print(sorted(numbers, key=lambda x: abs(x)))
<output>
[0, 1, 2, -2, -3, 5, -8]
</output>
```

## map(): apply a function to every element

`map()` applies a function to every element of a list, and `list(...)` collects the results into a new list:

```python
numbers = [1, 2, 3, 4, 5]

doubled = list(map(lambda x: x * 2, numbers))
print(doubled)
<output>
[2, 4, 6, 8, 10]
</output>

celsius = [0, 10, 20, 30]
fahrenheit = list(map(lambda c: c * 9 / 5 + 32, celsius))
print(fahrenheit)
<output>
[32.0, 50.0, 68.0, 86.0]
</output>
```

## filter(): keep elements that pass a condition

`filter()` keeps only the elements for which the function returned `True`:

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

even = list(filter(lambda x: x % 2 == 0, numbers))
print(even)
<output>
[2, 4, 6, 8, 10]
</output>

words = ["hi", "hello", "hey", "howdy"]
long_words = list(filter(lambda word: len(word) > 3, words))
print(long_words)
<output>
['hello', 'howdy']
</output>
```

## When to use a lambda, and when def

A lambda fits when the function is simple — a single expression — and is needed once, usually as an argument to `sorted()`, `map()` or `filter()`.

The limitations follow from the notation itself:

- the body is exactly one expression: no multiple lines, no assignments inside;
- there's no name, so you can't call it from anywhere else.

If the logic doesn't fit into one expression or is needed more than once — write a regular function with `def`: it has a name and room for several lines.

## Understanding check

**Which of the following statements about lambda functions in Python is true?**

1. Lambda functions can contain multiple expressions separated by semicolons — Lambda functions can contain only a single expression.

2. Lambda functions are always more efficient than regular functions — The efficiency of lambda functions and regular functions is practically the same.

3. **Correct answer:** Lambda functions are nameless and can contain only a single expression — Lambda functions have no name and are limited to one expression — which is why they are written right where they are needed.

4. You can use the assignment operator (=) inside a lambda function — Assignment is not allowed in a lambda: its body is a single expression, not statements.

In the next lesson — error handling: what to do when a program crashes, and how `try/except` turns a failure into a manageable situation.
