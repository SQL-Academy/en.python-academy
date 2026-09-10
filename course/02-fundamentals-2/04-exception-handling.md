---
meta:
    title: "Exception Handling in Python"
    description: "How to catch errors with try/except, what else and finally are for, and why you should not catch every exception at once."
---

# Exception Handling in Python

A program asks the user for their age and adds a year:

```python
age = int(input("How old are you? "))
print("Next year you'll be", age + 1)
```

As long as they type a number, everything works. But the moment the user types "forty" instead of `40`, `int()` can't turn it into a number: the program crashes with a `ValueError`, and the `print` line never runs. One bad input takes down the whole program.

Exception handling is for exactly these moments: catch the error, respond, and keep going, instead of crashing at the first surprise. We've postponed this conversation twice — in the lessons on text files and encapsulation. The time has come.

> An exception is an error that occurs while the program is running. By default it stops the program, but it can be caught and handled.

## The try-except block

The risky code goes into `try`, the reaction to the error into `except`:

```python
try:
    # code that might fail
except ExceptionType:
    # what to do if it failed
```

Here's what it looks like with a real error:

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Error: division by zero!")
<output>
Error: division by zero!
</output>
```

The program didn't crash: Python reached the division, saw the error, jumped into `except` and kept going.

## Different errors — different reactions

Different things can fail inside one `try`, and each exception type gets its own `except`. Python checks them in order and runs the first one that matches:

```python
file_name = "data.txt"

try:
    file = open(file_name, "r")
    number = int(file.readline().strip())
    print(f"Result: {100 / number}")
except FileNotFoundError:
    print(f"File {file_name} not found")
except ValueError:
    print("The file doesn't contain a number")
except ZeroDivisionError:
    print("The file contains zero — can't divide")
<output>
File data.txt not found
</output>
```

If the reaction to different errors is the same, the types are listed in parentheses, separated by commas:

```python
try:
    value = int("abc")
except (ValueError, ZeroDivisionError):
    print("An error occurred in the calculations")
<output>
An error occurred in the calculations
</output>
```

## The else and finally blocks

`else` runs only if there were no errors in `try`:

```python
try:
    number = int("42")
except ValueError:
    print("That's not a number")
else:
    print(f"Success! The number: {number}")
<output>
Success! The number: 42
</output>
```

`finally` runs always — error or no error:

```python
f = open("example.txt", "w")
try:
    f.write("Hello, world!")
finally:
    f.close()   # runs even if the write above fails
    print("File closed")
<output>
File closed
</output>
```

Notice that `open()` sits before `try`. Put it inside, and if the file fails to open, the variable `f` simply won't exist — and `f.close()` in `finally` will fail on its own. This is exactly the chore that the `with` statement from the files chapter automates: it closes the file for you.

`finally` is for whatever must happen no matter what — most often releasing resources like open files.

## Information about the error: as

With `as` you can get the exception object itself — for example, to show its message:

```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Message: {e}")
<output>
Message: division by zero
</output>
```

## Your own errors: raise

The `raise` statement raises an exception manually. You've already seen it in the encapsulation lesson: the setter refused to store a negative balance. Here's how it works:

```python
def check_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
    print(f"Age {age} accepted")

try:
    check_age(-5)
except ValueError as e:
    print(f"Error: {e}")
<output>
Error: Age cannot be negative
</output>
```

## Don't catch everything at once

A bare `except:` with no type catches any error — including ones you never suspected, like a typo in a variable name. The program "works" while the real problem stays silent:

```python
# Bad: no idea what actually happened
try:
    number = int("abc")
except:
    print("Some error")
<output>
Some error
</output>

# Good: catch exactly what you expect
try:
    number = int("abc")
except ValueError:
    print("Invalid number format")
<output>
Invalid number format
</output>
```

## Understanding check

**Which block always runs, whether an exception occurred or not?**

1. try — The try block contains code that may raise an exception, but it does not guarantee that all the code inside runs if an exception occurs.

2. except — The except block runs only when an exception of the matching type occurs.

3. else — The else block runs only if no exception occurred in the try block.

4. **Correct answer:** finally — The finally block always runs, exception or not. That is why it holds resource cleanup: closing files and database connections — things that must happen in any case.

In the next lesson — decorators. You've already met them twice: `@property` in encapsulation and `@abstractmethod` in polymorphism. It's time to see how the line with `@` works on the inside.
