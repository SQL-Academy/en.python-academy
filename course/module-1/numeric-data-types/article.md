---
meta:
    title: "Numeric data types in Python"
    description: "Integers (int) and floating-point numbers (float) in Python, arithmetic operations, rounding, and the subtleties of working with fractions."
---

# Numeric data types in Python

Python provides several types for working with numbers, which makes it a powerful tool for mathematical computations. In this article we'll go through the numeric data types and the operations you can perform on them.

## The main numeric types

Python has two main numeric types:

- **Integers** (int): numbers without a fractional part
- **Floating-point numbers** (float): numbers with a fractional part

There's also `complex` for imaginary numbers, but you'll almost never encounter it in regular development.

### Integers (int)

Integers (type `int`) represent numbers without a fractional part:

```python
# Positive and negative integers
positive = 42
negative = -73
zero = 0

big_number = 1234567890123456789012345678901234567890

# Underscore separator for readability of large numbers
million = 1_000_000  # The same as 1000000
```

Unlike many other languages, Python integers have unlimited precision: they can be arbitrarily large, limited only by available memory.

### Floating-point numbers (float)

Floating-point numbers (type `float`) represent real numbers with a fractional part:

```python
price = 19.99
pi_approx = 3.14159
negative_float = -0.5
```

The key thing to know about floats: their precision is limited, and seemingly simple calculations can produce small errors:

```python
result = 0.1 + 0.2
print(result)
<output>
0.30000000000000004
</output>
```

This isn't a Python bug, it's how fractional numbers are stored in binary: some decimal fractions can't be represented exactly. For financial calculations and other precision-critical tasks, use the `decimal` module.

For the same reason, **don't compare floats with `==` directly** — two seemingly equal fractions can differ in the last digit:

```python
print(0.1 + 0.2 == 0.3)
<output>
False
</output>
```

If you need a comparison with tolerance, use `math.isclose(a, b)`:

```python
import math
print(math.isclose(0.1 + 0.2, 0.3))
<output>
True
</output>
```

## Arithmetic operations

Python supports all the standard arithmetic operations:

```python
a = 10
b = 3

# Addition, subtraction, multiplication
a + b   # 13
a - b   # 7
a * b   # 30

# Division always returns a float
a / b   # 3.3333333333333335

# Integer division returns an int (drops the fractional part)
a // b  # 3

# Remainder (modulo)
a % b   # 1

# Exponentiation
a ** b  # 1000
```

### Mixed types

If an expression contains both `int` and `float`, the result is always `float`:

```python
5 + 3.14    # 8.14 (float)
10 * 2.0    # 20.0 (float)
4 ** 0.5    # 2.0  (float)
```

## Rounding

Python offers several ways to round numbers:

```python
# Round to the nearest integer
round(3.14159)      # 3

# Round to a specific number of decimal places
round(3.14159, 2)   # 3.14

import math

# Round down (toward smaller integer)
math.floor(3.99)    # 3

# Round up (toward larger integer)
math.ceil(3.01)     # 4

# Truncate the fractional part
math.trunc(3.99)    # 3
```

### Gotcha: round and banker's rounding

When a value lands exactly in the middle (e.g., `0.5` or `2.5`), Python rounds not to the larger integer but to the nearest **even** one. This is called _banker's rounding_ and is designed to reduce statistical bias when rounding large amounts of data:

```python
print(round(0.5))   # we'd expect 1
<output>
0
</output>
print(round(1.5))
<output>
2
</output>
print(round(2.5))   # we'd expect 3
<output>
2
</output>
print(round(3.5))
<output>
4
</output>
```

If you need "schoolbook" rounding (0.5 always goes up), write your own function or use `decimal` with an explicit rounding mode.

## Math functions

The `math` module gives you useful functions: `sqrt` (square root), constants `pi` and `e`:

```python
import math

math.sqrt(16)   # 4.0
math.pi         # 3.141592653589793
math.e          # 2.718281828459045
```

You'll also find trigonometry, logarithms, factorial and much more there. The full list is in the [math module documentation](https://docs.python.org/3/library/math.html).

## Built-in functions for numbers

Some numeric functions are built straight into Python, without an `import`:

```python
# Absolute value
abs(-10)              # 10

# Max and min
max(1, 5, 3, 9, 2)    # 9
min(1, 5, 3, 9, 2)    # 1

# Sum of a sequence
sum([1, 2, 3, 4, 5])  # 15

# Converting other types to numeric
int("42")             # 42
float("3.14")         # 3.14
```
