---
meta:
    title: "Conditional construct. The if statement"
    description: "Detailed explanation of conditional constructs in Python: syntax, usage options, nested conditions, and practical examples."
---

# Conditional construct. The if statement

A program doesn't always run in a straight line. Whether to let a user onto an "18+" page, show a discount, save a file or warn that it already exists — at each such fork you need to check a condition and take one path or another depending on the answer.

In Python you create a fork with the `if` statement: it checks a condition and runs a block of code only if the condition is true.

## The basic conditional construct

### Syntax of the if statement

The basic syntax of the `if` conditional construct in Python looks like this:

```python
# A simple if example
age = 18

if age >= 18:
    print("You are an adult.")
<output>
You are an adult.
</output>

print("This line runs no matter the condition.")
<output>
This line runs no matter the condition.
</output>
```

A few important details:

1. The condition is followed by a colon `:`
2. The block of code to execute when the condition is true **must** be indented (typically 4 spaces or one tab)
3. As soon as the indentation ends, the `if` block is over

Let's change the age and see what happens:

```python
age = 16

if age >= 18:
    print("You are an adult.")

print("This line runs no matter the condition.")
<output>
This line runs no matter the condition.
</output>
```

Here the condition `age >= 18` is not true (it's false), so the block inside `if` doesn't run, and we see only the second line.

### Using else (otherwise)

Often we want one block to run if the condition is true, and another if it's false. That's what `if-else` is for:

```python
temperature = 15

if temperature > 20:
    print("It's warm, a t-shirt will do.")
else:
    print("It's chilly, grab a jacket.")
<output>
It's chilly, grab a jacket.
</output>
```

If the temperature is above 20, the first block runs. Otherwise the block after `else` runs. In our example the temperature is 15, less than 20, so the `else` block runs.

### The elif clause (else if)

What if there are more than two cases to handle? For example, different messages for different temperature ranges. That's where `elif` (short for "else if") comes in:

```python
temperature = 25

if temperature < 0:
    print("Very cold. Put on a warm jacket and a hat.")
elif temperature < 10:
    print("Cold. Dress warmly.")
elif temperature < 20:
    print("Chilly. A light jacket wouldn't hurt.")
elif temperature < 30:
    print("Warm. A t-shirt and shorts will do.")
else:
    print("Hot. Bring some water.")
<output>
Warm. A t-shirt and shorts will do.
</output>
```

Python checks each condition in order. As soon as it finds the first true one, the corresponding block runs and all the remaining conditions are skipped. If none of them are true, the `else` block runs (if it's present).

> **Important:** Python checks conditions top to bottom and stops at the first one that's true. The order of conditions matters.

## More advanced conditionals

### Nested conditions

Conditional constructs can be nested inside one another to express more complex logic:

```python
age = 25
has_license = True

if age >= 18:
    print("You are an adult.")

    if has_license:
        print("You can drive a car.")
    else:
        print("To drive a car you need to get a license.")
else:
    print("You are a minor and cannot drive a car.")
<output>
You are an adult.
You can drive a car.
</output>
```

Don't overuse nested conditions, though: code with many levels of nesting becomes hard to read. Often the same logic can be rewritten using logical operators.

### Logical operators in conditions

To build more complex conditions, use logical operators:

- `and` (logical AND): the condition is true if both expressions are true
- `or` (logical OR): the condition is true if at least one expression is true
- `not` (logical NOT): inverts the value of the expression

Let's rewrite the nested-condition example:

```python
age = 25
has_license = True

if age >= 18 and has_license:
    print("You are an adult and can drive a car.")
elif age >= 18 and not has_license:
    print("You are an adult, but you need a license to drive.")
else:
    print("You are a minor and cannot drive a car.")
<output>
You are an adult and can drive a car.
</output>
```

Another example with the `or` operator:

```python
day = "Saturday"

if day == "Saturday" or day == "Sunday":
    print("It's the weekend, time to rest.")
else:
    print("It's a workday, off to work.")
<output>
It's the weekend, time to rest.
</output>
```

### The ternary operator

For simple conditions you can use the ternary operator — a compact form of `if-else`:

```python
age = 20

# Regular if-else form
if age >= 18:
    status = "an adult"
else:
    status = "a minor"

print(f"Regular form: You are {status}")
<output>
Regular form: You are an adult
</output>

# The same logic via the ternary operator
status = "an adult" if age >= 18 else "a minor"
print(f"Ternary: You are {status}")
<output>
Ternary: You are an adult
</output>
```

Ternary syntax:

```python
value_if_true if condition else value_if_false
```

Don't overuse it: complex conditions are easier to read in regular `if-else` form.

## Pythonic style for conditions

Python has a few idiomatic patterns for conditions that make code shorter and more readable. Get used to them early.

### Truthy checks

Beginners often write conditions with explicit comparisons to `True`, `False`, `0`, an empty string:

```python
if is_active == True:
if my_list == []:
if name == "":
```

From the boolean lesson we know that any value used in a condition is treated as `True` or `False` on its own. So these checks can be simplified:

```python
if is_active:        # already bool
if not my_list:      # empty list is falsy
if not name:         # empty string is falsy
```

Shorter and easier on the eye of any Python programmer.

### Chained comparisons

When you need to check whether a value is in a range, most languages require:

```python
if x >= 0 and x < 100:
```

Python lets you write the same as a single chain, just like in math:

```python
if 0 <= x < 100:
```

Same behaviour, reads more naturally. Especially handy with `elif` for ranges:

```python
if temperature < 0:
    ...
elif 0 <= temperature < 10:
    ...
elif 10 <= temperature < 20:
    ...
```

Now each branch is self-documenting: you can see exactly which range it handles, without having to remember what the previous branch already filtered out.

### The in operator for membership

When you need to check whether a value matches one of several options, you might write it with `or`:

```python
if day == "Saturday" or day == "Sunday":
    ...
```

The Pythonic version with `in` is shorter and more readable:

```python
if day in ("Saturday", "Sunday"):
    ...
```

The bigger the option set, the bigger the win: adding a third day is one tiny edit instead of another `or`.

## Practical examples

### Checking whether a number is even

A common use of conditions is checking the parity of a number:

```python
number = 42

if number % 2 == 0:
    print(f"{number} is even")
else:
    print(f"{number} is odd")
<output>
42 is even
</output>
```

### Password verification

Conditions help validate data against rules. Here `.isalpha()` returns `True` if the string consists only of letters, and `.isdigit()` returns `True` if it consists only of digits:

```python
password = "password123"

if len(password) < 8:
    print("Password is too short.")
elif password.isalpha():
    print("Password must contain more than just letters.")
elif password.isdigit():
    print("Password must contain more than just digits.")
else:
    print("Password is acceptable.")
<output>
Password is acceptable.
</output>
```

## Common beginner mistakes

### Confusing = and ==

In a condition you want `==` (comparison), not `=` (assignment). They are two different things:

```python
if x == 5:  # correct: checks whether x equals five
    ...

if x = 5:   # SyntaxError: you can't assign inside a condition
    ...
```

### Missing colon

After an `if`, `elif`, or `else` condition the `:` is required:

```python
if x > 5    # SyntaxError: missing colon
    print("greater than five")
```

### Inconsistent indentation

The body of a condition must be indented consistently. Changing the number of spaces inside one block, or mixing tabs and spaces, is not allowed:

```python
if x > 5:
    print("a")
      print("b")  # IndentationError: extra indentation
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

1. A — The condition x > 10 fails since x is 5.

2. B — The condition x > 5 fails since x is 5.

3. **Correct answer:** C — The condition x == 5 is true since x is indeed 5.

4. D — The else block runs only when all previous conditions are false, but x == 5 is true.
