# Numeric Data Types in Python

Python provides various types for working with numbers, making it a powerful tool for mathematical calculations. In this article, we'll examine in detail the numeric data types and operations that can be performed with them.

## Basic Numeric Types

Python has three main numeric types:

1. **Integers** (int) — numbers without a fractional part
2. **Floating-point numbers** (float) — numbers with a fractional part
3. **Complex numbers** (complex) — numbers with real and imaginary parts

### Integers (int)

Integers (type `int`) represent numbers without a fractional part:

```python
# Positive and negative integers
positive = 42
negative = -73
zero = 0

# Large numbers (Python has no limit on the size of integers)
big_number = 1234567890123456789012345678901234567890

# Binary, octal, and hexadecimal numbers
binary = 0b1010      # 10 in decimal
octal = 0o756        # 494 in decimal
hexadecimal = 0xFF   # 255 in decimal

# Using separator for easier reading of large numbers
million = 1_000_000  # This is the same as 1000000
```

Unlike many other programming languages, Python integers have unlimited precision, meaning they can be arbitrarily large (limited only by the available computer memory).

### Floating-point numbers (float)

Floating-point numbers (type `float`) represent real numbers with a fractional part:

```python
# Numbers with a decimal point
price = 19.99
pi_approx = 3.14159
negative_float = -0.5

# Exponential notation (scientific notation)
avogadro = 6.022e23  # 6.022 * 10^23
tiny = 1.6e-35      # 1.6 * 10^(-35)
```

Floating-point numbers have limited precision and can only represent approximate values of some decimal fractions. This can lead to small inaccuracies in calculations:

```python
# Example of limited float precision
result = 0.1 + 0.2
print(result)  # Will output 0.30000000000000004, not exactly 0.3
```

For financial calculations where precision is required, it's recommended to use the `decimal` module.

### Complex numbers (complex)

Complex numbers (type `complex`) contain real and imaginary parts, where the imaginary part is denoted by the suffix `j` or `J`:

```python
# Creating complex numbers
z1 = 3 + 4j
z2 = complex(2, -3)  # 2 - 3j

# Getting real and imaginary parts
real_part = z1.real  # 3.0
imag_part = z1.imag  # 4.0
```

Complex numbers are often used in engineering calculations, signal processing, and mathematical algorithms.

## Arithmetic Operations

Python supports all standard arithmetic operations for numeric types:

### Basic operations

```python
a = 10
b = 3

# Addition
sum_result = a + b  # 13

# Subtraction
diff_result = a - b  # 7

# Multiplication
mult_result = a * b  # 30

# Division (always returns float)
div_result = a / b  # 3.3333333333333335

# Integer division (returns the integer part of the division result)
floor_div = a // b  # 3

# Remainder of division
remainder = a % b  # 1

# Exponentiation
power = a ** b  # 1000
```

### Mixed operations

When performing operations between different numeric types, Python automatically converts types according to the following rule:
`int` → `float` → `complex`

```python
# int + float = float
mixed1 = 5 + 3.14  # 8.14 (float)

# float + complex = complex
mixed2 = 2.5 + 3j  # (2.5+3j) (complex)
```

## Rounding and Precision Control

Python provides several functions for rounding numbers:

```python
# Rounding to the nearest integer
rounded = round(3.14159)  # 3

# Rounding to a specified number of decimal places
rounded_pi = round(3.14159, 2)  # 3.14

# Rounding down (to the smaller integer)
import math
floor_value = math.floor(3.99)  # 3

# Rounding up (to the larger integer)
ceil_value = math.ceil(3.01)  # 4

# Truncating the fractional part
trunc_value = math.trunc(3.99)  # 3
```

## Mathematical Functions

The `math` module provides many useful mathematical functions:

```python
import math

# Constants
pi = math.pi      # 3.141592653589793
e = math.e        # 2.718281828459045

# Trigonometric functions (arguments in radians)
sin_value = math.sin(math.pi / 2)  # 1.0
cos_value = math.cos(math.pi)      # -1.0
tan_value = math.tan(math.pi / 4)  # 1.0

# Inverse trigonometric functions
asin_value = math.asin(1.0)  # 1.5707963267948966 (π/2)

# Logarithms
log_value = math.log(10)      # natural logarithm, ln(10) = 2.302585092994046
log10_value = math.log10(100) # logarithm base 10, log10(100) = 2.0

# Roots
sqrt_value = math.sqrt(16)    # square root, 4.0
```

For working with complex numbers, the `cmath` module is used, which contains similar functions but works with complex arguments.

## Random Numbers

The `random` module is used to generate random numbers in Python:

```python
import random

# Random number from 0 to 1
random_float = random.random()  # e.g., 0.7593696548501615

# Random integer in a range
random_int = random.randint(1, 10)  # random integer from 1 to 10 inclusive

# Random floating-point number in a range
random_range = random.uniform(1.5, 3.5)  # e.g., 2.4378123553729193

# Random choice from a sequence
random_choice = random.choice([1, 3, 5, 7, 9])  # random item from the list
```

## Numeric Methods and Functions

Python provides several built-in functions for working with numbers:

```python
# Absolute value
abs_value = abs(-10)  # 10

# Maximum and minimum value
max_value = max(1, 5, 3, 9, 2)  # 9
min_value = min(1, 5, 3, 9, 2)  # 1

# Sum of a sequence
sum_value = sum([1, 2, 3, 4, 5])  # 15

# Converting other types to numeric
int_from_str = int("42")     # 42
float_from_str = float("3.14")  # 3.14
```

## Understanding Check

Let's check your understanding of numeric data types:

**What will be the result of the expression `10 / 3` in Python?**
