---
meta:
    title: "Boolean data in Python"
    description: "Detailed description of the boolean data type in Python: creating boolean values, operations, type conversion, and practical usage examples."
---

# Boolean data in Python

Whenever a program decides what to do next, whether to print a message, whether to let a user in, whether to retry a request, it works with boolean values. Python's `bool` type holds exactly two values: `True` and `False`, and every condition and check is built from them.

## What is boolean data?

In Python, the values "true" and "false" are represented by the `bool` type, which can hold only two values:

- `True`
- `False`

Ask Python for the type and it confirms you're looking at a `bool`:

```python
is_raining = True
print(type(is_raining))
<output>
<class 'bool'>
</output>
```

> **Important:** `True` and `False` are always written with a capital first letter. If you write `true` or `false`, Python won't understand them and will throw an error.

## Boolean operators

We often need to combine several conditions. For example: "I'll go to the beach if it's sunny **AND** warm" or "I'll buy this phone if it's beautiful **OR** cheap". Python gives us three operators for this: `and`, `or`, `not`.

### The and operator (logical AND)

Returns `True` only if **both** values are true:

```python
sunny = True
warm = True

# Go to the beach if sunny AND warm
going_to_beach = sunny and warm
print(f"Sunny: {sunny}, Warm: {warm}")
<output>
Sunny: True, Warm: True
</output>
print(f"Going to the beach? {going_to_beach}")
<output>
Going to the beach? True
</output>

# What if the weather changes?
warm = False  # It got cold
going_to_beach = sunny and warm
print(f"Sunny: {sunny}, Warm: {warm}")
<output>
Sunny: True, Warm: False
</output>
print(f"Going to the beach? {going_to_beach}")
<output>
Going to the beach? False
</output>
```

### The or operator (logical OR)

Returns `True` if **at least one** value is true:

```python
phone_is_beautiful = True
phone_is_cheap = False

# Buy the phone if it's beautiful OR cheap
will_buy_phone = phone_is_beautiful or phone_is_cheap
print(f"Phone is beautiful: {phone_is_beautiful}, Phone is cheap: {phone_is_cheap}")
<output>
Phone is beautiful: True, Phone is cheap: False
</output>
print(f"Will we buy the phone? {will_buy_phone}")
<output>
Will we buy the phone? True
</output>
```

### The not operator (logical NOT)

Inverts the value: `True` becomes `False`, and vice versa:

```python
have_homework = True
print(f"I have homework: {have_homework}")
<output>
I have homework: True
</output>
print(f"I do NOT have homework: {not have_homework}")
<output>
I do NOT have homework: False
</output>
```

## Comparing values

Boolean values often come up as the result of a comparison:

```python
# Comparing numbers
my_age = 25
friend_age = 30

print(f"My age: {my_age}, friend's age: {friend_age}")
<output>
My age: 25, friend's age: 30
</output>
print(f"Are we the same age? {my_age == friend_age}")
<output>
Are we the same age? False
</output>
print(f"Are we different ages? {my_age != friend_age}")
<output>
Are we different ages? True
</output>
print(f"Am I younger? {my_age < friend_age}")
<output>
Am I younger? True
</output>

# Comparing strings (alphabetically)
print(f"'apple' < 'banana': {'apple' < 'banana'}")
<output>
'apple' < 'banana': True
</output>
```

### The is operator: comparing identity

Beyond `==`, Python also has the `is` operator. They look similar but check different things:

- `==` compares **values** (what's inside)
- `is` compares **identity** (whether two names point at the same object in memory)

```python
my_scores = [90, 85, 95]
friend_scores = [90, 85, 95]  # Same list, but a different object
same_list = my_scores         # The same object

print(f"Same content? {my_scores == friend_scores}")
<output>
Same content? True
</output>
print(f"The same object? {my_scores is friend_scores}")
<output>
The same object? False
</output>
print(f"Is same_list the same object as my_scores? {my_scores is same_list}")
<output>
Is same_list the same object as my_scores? True
</output>
```

**Rule of thumb:** in the vast majority of cases you want `==`. The `is` operator is appropriate only for checks against the special singletons: `is None`, `is True`, `is False`. Using `is` on numbers, strings, or lists is almost always a bug.

## Operator precedence: what's evaluated first?

Operators are applied in this order (from highest to lowest precedence):

1. `not` (highest precedence)
2. `and`
3. `or` (lowest precedence)

```python
# Precedence example
has_ticket = True
has_passport = False
has_visa = True

# Can we travel abroad?
# We need a ticket AND (passport OR visa)
can_travel = has_ticket and (has_passport or has_visa)

print(f"has_ticket and (has_passport or has_visa) = {can_travel}")
<output>
has_ticket and (has_passport or has_visa) = True
</output>

# Step-by-step evaluation:
step1 = has_passport or has_visa  # First, the expression in parentheses is evaluated
print(f"Step 1: has_passport or has_visa = {step1}")
<output>
Step 1: has_passport or has_visa = True
</output>

step2 = has_ticket and step1  # Then the and operator is applied
print(f"Step 2: has_ticket and (result of step 1) = {step2}")
<output>
Step 2: has_ticket and (result of step 1) = True
</output>
```

> **Tip:** if you're unsure about the order of evaluation, use parentheses. They make the code clearer and let you control the order explicitly.

## Conversion to boolean: what counts as true?

### The bool function

Python can convert any value to a boolean:

```python
print(bool(100))
<output>
True
</output>
print(bool(0))
<output>
False
</output>
print(bool("Hello"))
<output>
True
</output>
print(bool(""))
<output>
False
</output>
```

### What is considered true and false?

In Python most values are considered true (`True`).

The values considered false (`False`) are only:

- `False` (the boolean "no")
- `None` (absence of a value)
- Zero: `0`, `0.0`, `0j`
- Empty containers: `""`, `()`, `[]`, `{}`

```python
money = 0
if money:
    print("I have money")
else:
    print("My wallet is empty")
<output>
My wallet is empty
</output>

name = "Alex"
if name:
    print(f"Hello, {name}")
else:
    print("Hello, stranger")
<output>
Hello, Alex
</output>
```

## Understanding check

Let's check how well you've absorbed the material:

**What will the following expression return?**

```python
result = (False or True) and not (False and True or True)
```

1. True — Step by step: (False or True) = True; inside the second parens (False and True) = False, then False or True = True; not True = False; True and False = False. The correct answer is False.

2. **Correct answer:** False — Step by step: (False or True) = True; inside the second parens (False and True) = False, then False or True = True; not True = False; True and False = False.

3. None — Nothing here can produce None: the expression only involves True and False, so the result is one of those. Worth remembering for later though: the and/or operators return one of the values they are applied to rather than "truth in general" — had None been among them, it could well have come back.

4. Syntax error — This is a valid boolean expression in Python.
