# Conditional Constructs. If Statement

In this article, we'll get acquainted with conditional constructs in Python — one
of the most important tools in programming. Without them, our programs would be like a train
that can only move straight ahead, without the ability to choose a path at a fork.

Conditional constructs allow a program to "make decisions" and choose different actions depending on circumstances.

## What is a conditional construct?

A conditional construct is a tool that allows different blocks of code to be executed
depending on whether a certain condition is true (`True`) or false (`False`).

In Python, the main conditional construct is the `if` operator.
It checks if a condition is true, and if it is — executes the corresponding block of code.

## Basic conditional operator constructs

### If operator syntax

The basic syntax of the if conditional construct in Python looks like this:

```python
# Simple example of the if operator
age = 18

if age >= 18:
    print("You are an adult!")

print("This line will always execute, regardless of the condition.")
```

Note several important details:

1. A colon `:` is placed after the condition
2. The code block that should execute when the condition is true **must** have an indentation (usually 4 spaces or one tab)
3. Once the indentation ends, it marks the end of the if block

Let's change the age value and look at the result:

```python
age = 16

if age >= 18:
    print("You are an adult!")

print("This line will always execute, regardless of the condition.")
```

In this case, the condition `age >= 18` is not true (it's false), so the code block inside `if` doesn't execute, and we only see the second line.

### Using else

Often we need to execute one block of code if the condition is true, and another if the condition is false. For this, the `if-else` construct is used:

```python
temperature = 15

if temperature > 20:
    print("It's warm today, you can wear a t-shirt!")
else:
    print("It's cool today, take a jacket!")
```

Here, if the temperature is above 20, the first block will execute. Otherwise, the block after `else` executes. In our example, the temperature is 15, which is less than 20, so the code in the `else` block executes.

### The elif construct — "else if"

What if we have more than two choice options? For example, we want different messages for different temperature ranges. For this, the `elif` construct is used (short for "else if"):

```python
temperature = 25

if temperature < 0:
    print("Very cold! Wear a warm jacket and hat!")
elif temperature < 10:
    print("Cold. Dress warmly.")
elif temperature < 20:
    print("Cool. A light jacket wouldn't hurt.")
elif temperature < 30:
    print("Warm. T-shirt and shorts will do.")
else:
    print("Hot! Bring water with you!")
```

In this example, Python checks each condition in order. As soon as it finds the first true condition, it executes the corresponding code block, and all subsequent conditions are skipped. If none of the conditions are true, the `else` block executes (if present).

> **Important**: Python checks conditions from top to bottom and stops as soon as it finds the first true condition. The order of conditions matters!

## Advanced features of conditional constructs

### Nested conditions

Conditional constructs can be nested inside each other, creating more complex logic:

```python
age = 25
has_license = True

if age >= 18:
    print("You are an adult.")

    if has_license:
        print("You can drive a car.")
    else:
        print("To drive a car, you need to get a license.")
else:
    print("You are a minor and cannot drive a car.")
```

However, don't overuse nested conditions — code with many levels of nesting becomes difficult to read. Often such logic can be rewritten using logical operators.

### Logical operators in conditions

Logical operators are used to create complex conditions:

-   `and` — logical AND (condition is true if both expressions are true)
-   `or` — logical OR (condition is true if at least one expression is true)
-   `not` — logical NOT (inverts the value of the expression)

Let's rewrite our example with nested conditions:

```python
age = 25
has_license = True

if age >= 18 and has_license:
    print("You are an adult and can drive a car.")
elif age >= 18 and not has_license:
    print("You are an adult, but to drive a car you need to get a license.")
else:
    print("You are a minor and cannot drive a car.")
```

Such code is more readable and easier to maintain.

Let's look at another example with the `or` operator:

```python
day = "Saturday"

if day == "Saturday" or day == "Sunday":
    print("It's a weekend! You can relax. 😎")
else:
    print("It's a workday. Need to go to work. 💼")
```

### Ternary operator

For simple conditions, you can use the ternary operator — a compact form of writing a condition:

```python
age = 20

# Regular notation using if-else
if age >= 18:
    status = "an adult"
else:
    status = "a minor"

print(f"Regular notation: You are {status}")

# The same logic, but using the ternary operator
status = "an adult" if age >= 18 else "a minor"
print(f"Ternary operator: You are {status}")
```

The syntax of the ternary operator:

```python
value_if_true if condition else value_if_false
```

The ternary operator makes the code more compact, but don't abuse it — complex conditions are better written in the regular format for readability.

## Practical examples of using conditions

Conditional constructs are widely used in everyday programming. Let's look at several practical examples:

### Checking if a number is even

One of the simple and frequently used applications of conditions is determining if a number is even:

```python
number = 42

if number % 2 == 0:
    print(f"{number} is an even number")
else:
    print(f"{number} is an odd number")
```

### Password verification

Conditions help implement data verification against certain criteria:

```python
password = "password123"

if len(password) < 8:
    print("Password is too short!")
elif password.isalpha():
    print("Password must contain not only letters!")
elif password.isdigit():
    print("Password must contain not only digits!")
else:
    print("Password is suitable!")
```

## Understanding check

Let's check how well you've understood the topic of conditional constructs:

**What will the following code output?**

```python
x = 5
if x > 10:
    print("A")
elif x > 5:
    print("B")
elif x == 5:
    print("C")
else:
    print("D")
```
