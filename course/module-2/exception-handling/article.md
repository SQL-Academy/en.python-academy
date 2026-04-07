# Exception Handling in Python

Traveling through unknown territory requires careful preparation — an experienced explorer thinks through possible dangers in advance, checks the map, prepares equipment, and plans the route. In programming, exception handling works in a similar way — it's a reliable tool that allows a program to properly respond to unexpected situations and continue working, instead of crashing at the first error. 🧭

## What are exceptions?

> Exceptions are events that disrupt the normal flow of program execution, which can be handled by special language constructs.

## Why do we need exception handling?

Exception handling allows you to:

1. **Prevent program crashes** — your program will continue running even if something goes wrong
2. **Separate error handling code from the main logic** — makes code more readable and understandable
3. **Handle different types of errors centrally** — simplifies program structure
4. **Make your program more robust** — it can work correctly even in unexpected situations

## Try-except blocks

The main mechanism for exception handling in Python is the `try-except` block. It allows you to:

-   Isolate potentially dangerous code
-   Catch errors
-   Perform alternative actions
-   Continue program execution

### Try-except block syntax

```python
try:
    # Risky code that might raise an exception
except ExceptionType:
    # Code that will execute when the specified exception occurs
```

Here's a simple example:

```python
try:
    # Code that might raise an exception
    result = 10 / 0  # Division by zero will raise ZeroDivisionError
except ZeroDivisionError:
    # Code that will execute when the exception occurs
    print("Error: division by zero!")
```

### Complete try-except block structure

The complete structure of a try-except block includes additional sections:

```python
try:
    # Risky code
except ExceptionType1:
    # Handle exception type 1
except ExceptionType2:
    # Handle exception type 2
else:
    # Executes if no exceptions occurred in the try block
finally:
    # Always executes, regardless of whether exceptions occurred
```

## Handling different types of exceptions

### Sequential handling of different exceptions

You can handle different types of exceptions differently:

```python
try:
    # Several operations that might raise different exceptions
    file_name = input("Enter file name: ")  # Let's assume "data.txt" was entered
    file = open(file_name, "r")
    line = file.readline()
    number = int(line.strip())
    result = 100 / number
    print(f"Result: {result}")
except FileNotFoundError:
    print(f"File {file_name} not found")
except ValueError:
    print("Cannot convert data to a number")
except ZeroDivisionError:
    print("Error: division by zero")
except Exception as e:
    print(f"An unknown error occurred: {e}")
```

Important: Python checks the `except` blocks in the order they are declared and executes the first matching block.

### Handling multiple exceptions with one block

If you need the same handling for different exceptions:

```python
try:
    value = int("abc")
    result = 10 / 0
except (ValueError, ZeroDivisionError):
    print("A calculation error occurred")
```

## Else and finally blocks

### Else block

The `else` block executes only if no exceptions were raised in the `try` block:

```python
try:
    number = int("42")  # This will execute successfully
except ValueError:
    print("This is not a number")
else:
    # This block will execute since no exception occurred
    print(f"Success! Number: {number}")
    print(f"Square of the number: {number ** 2}")
```

The `else` block is useful when you need to execute code only after a risky operation has completed successfully.

### Finally block

The `finally` block always executes, regardless of whether an exception occurred or not:

```python
try:
    f = open("example.txt", "w")
    f.write("Hello, world!")
    # Let's assume an exception might occur here
except IOError:
    print("An I/O error occurred")
finally:
    print("Closing file")
    f.close()  # The file will be closed in any case
```

The `finally` block is typically used for:

-   Releasing resources (closing files, database connections)
-   Cleaning up temporary data
-   Logging the completion of an operation

## Getting information about an exception

Use `as` to get the exception object:

```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Exception type: {type(e).__name__}")
    print(f"Message: {e}")
```

## Raising exceptions

You can raise exceptions yourself using the `raise` statement:

```python
def check_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
    if age < 18:
        print("You are a minor")
    else:
        print("You are an adult")

try:
    check_age(-5)
except ValueError as e:
    print(f"Error: {e}")
```

## Creating custom exceptions

You can create your own exception classes by inheriting from standard ones:

```python
# Creating a custom exception class
class InvalidEmailError(Exception):
    """Raised when an email doesn't match the format"""
    pass

def validate_email(email):
    if "@" not in email:
        raise InvalidEmailError("Email must contain the @ symbol")
    print(f"Email {email} is valid")

try:
    validate_email("user.example.com")
except InvalidEmailError as e:
    print(f"Validation error: {e}")
```

## Practical recommendations

### 1. Specify concrete exceptions

```python
# Bad:
try:
    number = int("abc")
except:  # Catches all exceptions
    print("Error")

# Good:
try:
    number = int("abc")
except ValueError:
    print("Invalid number format")
```

### 2. Minimize code in the try block

```python
# Good:
try:
    file = open("data.txt", "r")
except FileNotFoundError:
    print("File not found")
    file = None

if file:
    try:
        content = file.read()
    except:
        print("Error reading file")
    finally:
        file.close()
```

### 3. Use else and finally correctly

```python
def get_value_from_list(my_list, index):
    try:
        value = my_list[index]
    except IndexError:
        print(f"Index {index} out of range")
        return None
    else:
        # This code executes only if no exception occurred
        print(f"Value successfully retrieved")
        return value
    finally:
        # This code always executes
        print("List access operation completed")

result = get_value_from_list([1, 2, 3], 1)
print(f"Result: {result}")

result = get_value_from_list([1, 2, 3], 10)
print(f"Result: {result}")
```

## Understanding check

**Which block always executes, regardless of whether an exception occurred or not?**


## Conclusion

Exception handling is your reliable protector against chaos in code. It turns potential disasters into controlled situations, allowing your programs to elegantly handle unexpected events. By properly using try-except, else, and finally, you create more reliable and robust code. 🛡️
