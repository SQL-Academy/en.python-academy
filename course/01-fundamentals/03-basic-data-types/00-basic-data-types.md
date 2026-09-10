---
meta:
    title: "Basic Data Types in Python"
    description: "Why Python attaches a type to every value: how the same operator behaves differently, how to inspect a type with type, and an overview of built-in types."
---

# Basic Data Types in Python

Look at these two lines. The operator is the same, but the result is different:

```python
print(10 + 5)
<output>
15
</output>

print("10" + "5")
<output>
105
</output>
```

In the first case `+` added the numbers; in the second it glued two strings together. Python behaves differently because `10` and `"10"` are not the same thing to it: the first is a number, the second is text. Python attaches a **type** to every value, and it's the type that decides what an operation on that value means.

That's why mixing up types is a common source of beginner bugs: you add two values expecting 15 and get "105" instead, because the values were actually strings.

## How to find out a value's type

If you're not sure what you're dealing with, you can ask for the type directly with the `type()` function.

```python
print(type(42))
<output>
<class 'int'>
</output>

print(type(3.14))
<output>
<class 'float'>
</output>

print(type("Python"))
<output>
<class 'str'>
</output>

print(type(True))
<output>
<class 'bool'>
</output>
```

In the output `int` is a whole number, `float` is a number with a fractional part, `str` is a string, `bool` is a boolean value (true or false). You don't declare the type up front: Python figures it out from what you wrote.

```python
number = 42        # int  — a whole number
pi = 3.14          # float — a number with a fractional part
name = "Python"    # str  — a string
is_active = True   # bool — a boolean value
```

## What types exist in Python

Besides the four basic ones, Python has built-in types for collections and more specialized tasks. Here are the main categories:

| Category         | Types                          |
| ---------------- | ------------------------------ |
| Text             | `str`                          |
| Numbers          | `int`, `float`                 |
| Boolean          | `bool`                         |
| Collections      | `list`, `tuple`, `set`, `dict` |
| Absence of value | `NoneType` (the value `None`)  |

In the next few chapters we'll cover numbers, strings, and booleans in detail, then move on to collections — lists, tuples, sets, and dictionaries. For now one idea is enough: every value has a type, and that type decides what you can do with the value.

## Check your understanding

**What does `print("3" + "4")` output?**

1. 7 — The numbers would add up to 7 if they were numbers: 3 + 4. But in quotes these are strings, and for strings + means joining, not addition.

2. **Correct answer:** 34 — For strings the + operator joins them one after another, so "3" and "4" give "34". To get 7 the values would have to be numbers: 3 + 4.

3. An error: you can't add strings — Adding strings is in fact allowed — it's the join operation. An error would come from adding a string to a number, for example "3" + 4.
