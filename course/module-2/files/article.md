# Working with Files in Python

Now we'll cover one of the most fundamental capabilities in programming — working
with files.
The ability to correctly read data from files and write it back
will be useful in virtually any project. 📁

Files are containers for storing data on disk.
Python provides powerful and convenient tools for working with them. Let's figure out how to use these tools!

## Basic File Operations

In Python, working with files usually includes the following steps:

1. **Opening a file** — specify the file and the mode of working with it
2. **Reading or writing** — perform the necessary operations with the file
3. **Closing the file** — free up system resources

Let's consider each of these steps in more detail.

## Opening Files

To open a file in Python, the `open()` function is used.
It takes at least two parameters: the path to the file and the opening mode.

> The `open(file, mode)` function returns a file object that can be used for reading, writing, or other operations with the file.

Here are the basic file opening modes:

| Mode  | Description                                                |
| ----- | ---------------------------------------------------------- |
| `'r'` | Reading (default mode)                                     |
| `'w'` | Writing (creates a new file or overwrites an existing one) |
| `'a'` | Appending — adds data to the end of the file               |
| `'b'` | Binary mode (e.g., `'rb'` for reading a binary file)       |
| `'t'` | Text mode (default mode)                                   |
| `'+'` | Updating (reading and writing)                             |

Examples of opening files in different modes:

```python
# Opening a file for reading (default mode - 'r')
file = open('example.txt', 'r')
print(f"File opened in mode: {file.mode}")
file.close()

# Opening a file for writing
file = open('new_file.txt', 'w')
print(f"File opened in mode: {file.mode}")
file.close()

# Opening a binary file for reading
file = open('image.jpg', 'rb')
print(f"File opened in mode: {file.mode}")
file.close()
```

## Reading from a File

After opening a file, its contents can be read in several ways:

### Reading the entire file

```python
# Let's create a test file
with open('sample.txt', 'w') as f:
    f.write("First line\nSecond line\nThird line")

# Reading the entire file at once
with open('sample.txt', 'r') as file:
    content = file.read()
    print("File contents:")
    print(content)
```

### Reading a file line by line

```python
# Reading a file line by line using a loop
with open('sample.txt', 'r') as file:
    print("Reading line by line:")
    for line in file:
        print(f"  Line: {line.strip()}")
```

### Reading a specific number of characters

```python
# Reading a specific number of characters
with open('sample.txt', 'r') as file:
    first_10_chars = file.read(10)
    print(f"First 10 characters: {first_10_chars}")

    # Reading the next 10 characters
    next_10_chars = file.read(10)
    print(f"Next 10 characters: {next_10_chars}")
```

### Reading all lines into a list

```python
# Reading all lines into a list
with open('sample.txt', 'r') as file:
    lines = file.readlines()
    print(f"List of lines: {lines}")
```

## Writing to a File

Writing data to a file can also be done in different ways:

### Writing a string

```python
# Writing a string to a file
with open('output.txt', 'w') as file:
    file.write("Hello, world!\n")
    file.write("Python is a great programming language.")

# Let's check what was written
with open('output.txt', 'r') as file:
    content = file.read()
    print("File contents after writing:")
    print(content)
```

### Writing multiple lines

```python
# Writing a list of strings to a file
lines = ["First line", "Second line", "Third line"]

with open('lines.txt', 'w') as file:
    for line in lines:
        file.write(line + '\n')

# Alternative way: using writelines()
with open('lines2.txt', 'w') as file:
    # Don't forget to add newline characters
    file.writelines([line + '\n' for line in lines])

# Let's check the second file
with open('lines2.txt', 'r') as file:
    content = file.read()
    print("Contents of lines2.txt:")
    print(content)
```

### Appending data to the end of a file

```python
# Appending data to the end of a file (mode 'a')
with open('output.txt', 'a') as file:
    file.write("\nThis line was added later.")

# Let's check the result
with open('output.txt', 'r') as file:
    content = file.read()
    print("File contents after appending:")
    print(content)
```

## The with Context Manager

You might have noticed that in all the examples above, we used the `with open(...) as file:` construct.
This is a context manager that automatically closes the file after finishing working
with it.

> The `with` context manager is a safe way to work with files, which guarantees that the file will be closed even if errors occur.

Let's compare two approaches:

```python
# Traditional approach (requires explicit file closing)
file = open('example.txt', 'w')
try:
    file.write("Example text")
finally:
    file.close()  # The file needs to be closed explicitly
    print("File closed manually")

# Approach using with (safer)
with open('example.txt', 'w') as file:
    file.write("Example text")
# The file is automatically closed after exiting the with block
print("File automatically closed after the with block")
```

It is strongly recommended to always use the `with` context manager when working with files in Python! 👍

## Exception Handling When Working with Files

Various errors can occur when working with files:

-   The file doesn't exist
-   Insufficient permissions to access the file
-   The disk is full
-   Etc.

It's good practice to handle these errors:

```python
# Handling possible errors when opening a file
try:
    with open('non_existent_file.txt', 'r') as file:
        content = file.read()
except FileNotFoundError:
    print("Error: File not found!")
except PermissionError:
    print("Error: Insufficient permissions to access the file!")
except Exception as e:
    print(f"An error occurred: {e}")
```

## Additional File Operations

Python provides many additional capabilities for working with files:

### Moving the Pointer

```python
# Moving the pointer in a file
with open('sample.txt', 'r') as file:
    # Let's read the first 5 characters
    print(file.read(5))

    # Let's move the pointer to the beginning of the file
    file.seek(0)
    print("After file.seek(0):", file.read(5))

    # Let's move the pointer to the 6th position
    file.seek(6)
    print("After file.seek(6):", file.read(5))
```

### Getting the Current Position

```python
# Getting the current pointer position
with open('sample.txt', 'r') as file:
    print(f"Initial position: {file.tell()}")
    file.read(10)
    print(f"Position after reading 10 characters: {file.tell()}")
    file.readline()
    print(f"Position after reading a line: {file.tell()}")
```

## Working with File Paths

When working with files, it's important to specify paths correctly. Python provides the `os.path` module and the `pathlib` module to make working with paths easier:

```python
import os

# Current working directory
current_dir = os.getcwd()
print(f"Current directory: {current_dir}")

# Joining paths (correctly handling separators)
data_file = os.path.join(current_dir, 'data', 'info.txt')
print(f"Path to file: {data_file}")

# Checking if a file exists
sample_exists = os.path.exists('sample.txt')
print(f"File sample.txt exists: {sample_exists}")

# Getting the filename and extension
filename = "path/to/document.pdf"
basename = os.path.basename(filename)
name, ext = os.path.splitext(basename)
print(f"Filename: {name}, extension: {ext}")
```

A more modern approach using `pathlib`:

```python
from pathlib import Path

# Current directory
current_path = Path.cwd()
print(f"Current directory: {current_path}")

# Creating a path
data_file = current_path / 'data' / 'info.txt'
print(f"Path to file: {data_file}")

# Checking if a file exists
sample_path = Path('sample.txt')
print(f"File sample.txt exists: {sample_path.exists()}")

# Getting the filename and extension
document_path = Path("path/to/document.pdf")
print(f"Filename: {document_path.stem}, extension: {document_path.suffix}")
```

## Understanding Check

Let's check how well you've understood the topic of working with files:

**Which code correctly opens a file for writing and appends a string to the end of the file?**


In future lessons, we'll delve deeper into working with specific file types, such as text files, CSV, JSON, and others.
