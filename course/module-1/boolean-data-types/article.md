# Boolean Data in Python

Let's dive into the world of boolean data in Python.
This topic may seem simple, but it's incredibly important — after all, it's with boolean values that your programs will make decisions! 🧠

## What is boolean data?

In everyday life, we often ask questions with "yes" or "no" answers.  
For example: "Is it raining today?", "Do you like pizza?", "Is 2 + 2 = 5?".

In Python, there's a special data type for such answers — `bool`, which can only take two values:

-   `True` (truth, "yes") ✅
-   `False` (falsehood, "no") ❌

```python
# Creating boolean variables
is_raining = True  # Yes, it's raining
likes_pizza = True  # Yes, I like pizza

print(type(is_raining))  # <class 'bool'>
```

> 💡 **Important**: `True` and `False` are always written with a capital letter.
> If you write `true` or `false`, Python won't understand and will raise an error!

## Boolean operators: how to combine conditions

In life, we often combine multiple conditions.  
For example: "I'll go to the beach if it's sunny **AND** warm" or "I'll buy this phone if it's beautiful **OR** inexpensive".

Python gives us three main logical operators:

### 1. The and operator (logical AND)

Returns `True` only if **both** values are true:

```python
sunny = True  # It's sunny
warm = True   # It's warm

# I'll go to the beach if it's sunny AND warm
going_to_beach = sunny and warm
print(f"Sunny: {sunny}, Warm: {warm}")
print(f"Going to the beach? {going_to_beach}")

# What if the weather changes?
warm = False  # It got cold
going_to_beach = sunny and warm
print(f"Sunny: {sunny}, Warm: {warm}")
print(f"Going to the beach? {going_to_beach}")
```

### 2. The or operator (logical OR)

Returns `True` if **at least one** value is true:

```python
phone_is_beautiful = True   # The phone is beautiful
phone_is_cheap = False      # But not cheap

# I'll buy the phone if it's beautiful OR inexpensive
will_buy_phone = phone_is_beautiful or phone_is_cheap
print(f"Phone is beautiful: {phone_is_beautiful}, Phone is cheap: {phone_is_cheap}")
print(f"Will buy the phone? {will_buy_phone}")
```

Truth table for the `or` operator:

```python
print("True or True =", True or True)
print("True or False =", True or False)
print("False or True =", False or True)
print("False or False =", False or False)
```

### 3. The not operator (logical NOT)

Inverts the value: `True` becomes `False`, and vice versa:

```python
have_homework = True
print(f"I have homework: {have_homework}")
print(f"I DON'T have homework: {not have_homework}")

# Another example: if it's not raining, we go for a walk
is_raining = False
going_for_a_walk = not is_raining
print(f"Is it raining? {is_raining}")
print(f"Going for a walk? {going_for_a_walk}")
```

## Comparing values

Boolean values often appear as a result of comparison:

```python
# Comparing numbers
my_age = 25
friend_age = 30

print(f"My age: {my_age}, friend's age: {friend_age}")
print(f"Our ages are the same? {my_age == friend_age}")
print(f"Our ages are different? {my_age != friend_age}")
print(f"Am I younger? {my_age < friend_age}")
print(f"Am I older? {my_age > friend_age}")

# Comparing strings (alphabetically)
print("String comparison:")
print(f"'apple' < 'banana': {'apple' < 'banana'}")
print(f"'python' == 'Python': {'python' == 'Python'}")

# Comparing lists
my_scores = [90, 85, 95]
friend_scores = [90, 85, 95]  # Same list, but different object
same_list = my_scores  # The same object

print("List comparison:")
print(f"Contents the same? {my_scores == friend_scores}")
print(f"Is it the same object? {my_scores is friend_scores}")
print(f"Is same_list the same object as my_scores? {my_scores is same_list}")
```

## Operator precedence: what gets calculated first?

Operators are executed in the following order (from highest to lowest):

1. `not` (highest priority)
2. `and`
3. `or` (lowest priority)

```python
# Example with priorities
has_ticket = True
has_passport = False
has_visa = True

print(f"Have ticket: {has_ticket}")
print(f"Have passport: {has_passport}")
print(f"Have visa: {has_visa}")

# Can we travel abroad?
# Need a ticket AND (passport OR visa)
can_travel = has_ticket and (has_passport or has_visa)
print("\nCan we travel abroad?")
print(f"has_ticket and (has_passport or has_visa) = {can_travel}")

# Let's break down the calculation step by step:
step1 = has_passport or has_visa  # First, the expression in parentheses is calculated
print(f"\nStep 1: has_passport or has_visa = {step1}")

step2 = has_ticket and step1  # Then the and operator is applied
print(f"Step 2: has_ticket and (result of step 1) = {step2}")
```

> 💡 **Tip**: If you're unsure about the order of execution, use parentheses!
> They make the code clearer and precisely control the order of calculations.

## Converting to boolean type: what counts as true?

### The bool function

Python can convert any value to a boolean type:

```python
print(bool(100))
print(bool(0))
print(bool("Hello"))
print(bool(""))
```

### What counts as true and false?

In Python, most values are considered true (`True`).

Only the following are considered false (`False`):

-   `False` (the logical "no")
-   `None` (absence of value)
-   Zeros: `0`, `0.0`, `0j`
-   Empty containers: `""`, `()`, `[]`, `{}`

```python
# Examples in conditions
money = 0
if money:
    print("I have money!")
else:
    print("My wallet is empty :(")  # This will be printed

name = "Alex"
if name:
    print(f"Hello, {name}!")  # This will be printed
else:
    print("Hello, stranger!")
```

## Understanding check

Let's check how well you've understood the material:

**What will the following expression return?**

```python
result = (False or True) and not (False and True or True)
```


Now you know how boolean data works in Python.
Although they can only take two values — `True` and `False`,
their importance in programming cannot be overstated.
