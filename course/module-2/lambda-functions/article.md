# Lambda Functions in Python

> A lambda function is a small anonymous function that can take any number of arguments but contains only one expression.

Translated from programmer language to human language:

-   **Anonymous** — it has no name (unlike regular functions defined with `def`)
-   **Small** — it's limited to one expression (one line of code)
-   **Function** — it takes arguments and returns a result

### Lambda function syntax

```python
lambda arguments: expression
```

Where:

-   `lambda` — is the keyword that tells Python we're creating a lambda function
-   `arguments` — are the input parameters (can be from 0 to several, separated by commas)
-   `expression` — is a single expression, the result of which will be returned

## Comparison with regular functions

Let's compare a regular function and a lambda function that do the same thing:

```python
# Regular function
def square(x):
    return x * x

# Equivalent lambda function
square_lambda = lambda x: x * x

# Using both functions
print(square(5))
print(square_lambda(5))

# Function with multiple arguments
def power(base, exponent):
    return base ** exponent

# Equivalent lambda function
power_lambda = lambda base, exponent: base ** exponent

print(power(2, 3))
print(power_lambda(2, 3))
```

As you can see, lambda functions are more compact but less readable for complex operations.

## When to use lambda functions?

Lambda functions are best suited for cases where:

1. The function is simple (one expression)
2. The function is used only once (or a few times in one place)
3. The function is passed as an argument to another function

The most common use cases are with higher-order functions such as `map()`, `filter()`, `sorted()`, etc.

## Lambda with higher-order functions

### map() — applying a function to each element

The `map()` function applies the specified function to each element of an iterable object:

```python
# Doubling all numbers in a list
numbers = [1, 2, 3, 4, 5]

# With a regular function
def double(x):
    return x * 2

doubled = list(map(double, numbers))
print(doubled)

# With a lambda function (much more compact!)
doubled_lambda = list(map(lambda x: x * 2, numbers))
print(doubled_lambda)

# Converting temperature from Celsius to Fahrenheit
celsius = [0, 10, 20, 30, 40]
fahrenheit = list(map(lambda c: (c * 9/5) + 32, celsius))
print(fahrenheit)
```

### filter() — selecting elements by condition

The `filter()` function creates an iterator from elements for which the function returns `True`:

```python
# Filtering even numbers
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# With a lambda function
even_lambda = list(filter(lambda x: x % 2 == 0, numbers))
print(even_lambda)

# Filtering words longer than 3 letters
words = ["hi", "hello", "hey", "howdy", "hi there"]
long_words = list(filter(lambda word: len(word) > 3, words))
print(long_words)
```

### sorted() — sorting with a custom key

The `sorted()` function returns a sorted list, and with the `key` parameter you can specify a function to extract the value for comparison:

```python
# Sorting numbers by absolute value
numbers = [5, -3, 2, -8, 1, 0, -2]

sorted_numbers = sorted(numbers, key=lambda x: abs(x))
print(sorted_numbers)

# Sorting dictionaries by the value of a specific key
students = [
    {"name": "Alice", "grade": 85},
    {"name": "Bob", "grade": 92},
    {"name": "Charlie", "grade": 78},
    {"name": "Diana", "grade": 95}
]

# Sorting by grade (descending)
sorted_by_grade = sorted(students, key=lambda student: student["grade"], reverse=True)
for student in sorted_by_grade:
    print(f"{student['name']}: {student['grade']}")
```

### reduce() — sequential application of a function

The `reduce()` function (from the `functools` module) sequentially applies a function to elements, accumulating the result:

```python
from functools import reduce

# Sum of all numbers in a list
numbers = [1, 2, 3, 4, 5]

total_lambda = reduce(lambda x, y: x + y, numbers)
print(total_lambda)

# Joining strings
words = ["Hello", "world", "of", "Python"]
sentence = reduce(lambda x, y: x + " " + y, words)
print(sentence)
```

## Passing lambda functions to other functions

Lambda functions are often used as arguments in other functions:

```python
def apply_operation(x, y, operation):
    """Applies an operation to two numbers and returns the result"""
    return operation(x, y)

# Using with different lambda functions
print(apply_operation(5, 3, lambda x, y: x + y))  # Addition
print(apply_operation(5, 3, lambda x, y: x * y))  # Multiplication

# Formatting data
def format_data(data, formatter):
    """Formats data using the specified function"""
    return [formatter(item) for item in data]

names = ["alice", "bob", "charlie"]
print(format_data(names, lambda x: x.title()))  # Capitalize first letter
```

## Limitations of lambda functions

Lambda functions have several important limitations:

1. **Only one expression** — you cannot use multiple lines of code
2. **No documentation** — you cannot add a docstring
3. **Limited readability** — regular functions are better for complex operations
4. **No assignment operator** — you cannot use `=` in a lambda

```python
# Example of complex logic where it's better not to use lambda
complex_lambda = lambda x: (
    x ** 2 if x > 0
    else x + 1 if x < 0
    else 42
)
print(complex_lambda(5))

# The same thing but with a regular function - much clearer
def process_number(x):
    """Processes a number according to rules:
    - Positive -> square
    - Negative -> x + 1
    - Zero -> 42
    """
    if x > 0:
        return x ** 2
    elif x < 0:
        return x + 1
    else:
        return 42

print(process_number(5))
```

## Understanding check

**Which of the following statements about lambda functions in Python is true?**
