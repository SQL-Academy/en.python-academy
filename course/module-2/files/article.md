---
meta:
    title: "Working with Files in Python"
    description: "How to open, read, write, and close files in Python. Basic concepts and methods for working with the file system."
---

# Working with Files in Python

Any variable lives only while the program is running: close it, and your to-do list, the score you racked up, your saved settings are gone. To make data survive a restart, you write it to a file on disk and read it back next time. That's what notes, game saves, report exports (almost any application) rest on.

## Opening Files

To open a file in Python, the `open()` function is used.
It takes at least two parameters: the path to the file and the opening mode.

> The `open(file, mode)` function returns a file object that can be used for reading, writing, or other operations with the file.

The mode is a short string that answers "what are we going to do with this file". You pick one of three:

| Letter | What it does                                                |
| ------ | ----------------------------------------------------------- |
| `'r'`  | read; the file must already exist (the default mode)        |
| `'w'`  | write from scratch: creates the file, wipes an existing one |
| `'a'`  | append to the end; the old contents stay where they are     |

Let's see the two most common modes in action:

```python
# 'w' creates the file (or overwrites it) and opens it for writing
file = open('notes.txt', 'w')
file.write("Buy milk")
file.close()

# 'r' opens an existing file for reading
file = open('notes.txt', 'r')
print(file.read())
<output>
Buy milk
</output>
file.close()
```

`'w'` deserves a separate warning: it wipes the old contents silently, with no confirmation. Open a file full of important data with `'w'` instead of `'a'`, and the data is gone. `'r'` is the opposite: it requires the file to already exist, and if it doesn't, Python stops the program with a `FileNotFoundError`.

## The with Context Manager

In the example above, `close()` was called after the work with the file was done. Let's see why that call is needed and why real code almost never writes it.

An open file has to be closed: while it's open it holds system resources, and written data may not reach the disk until it's closed. You can close it manually:

```python
file = open('example.txt', 'w')
file.write("Example text")
file.close()
```

The catch is that it's easy to forget `close()`. And if something goes wrong between opening and closing and the program is interrupted, `close()` is never reached, and the file stays open.

The `with` construct takes this off your hands: it closes the file when the block ends, no matter the outcome.

```python
with open('example.txt', 'w') as file:
    file.write("Example text")
# the file is already closed here automatically
print("File automatically closed after the with block")
<output>
File automatically closed after the with block
</output>
```

> The `with` context manager guarantees the file is closed even if an error occurs inside the block. It's the standard way to work with files in Python, and it's what we use from here on.

## Reading from a File

After opening a file, its contents can be read in several ways.

In all the examples below, a `sample.txt` file with three lines sits next to the program.

### Reading the entire file

**sample.txt**

```text
First line
Second line
Third line
```

**main.py**

```python
with open('sample.txt', 'r') as file:
    content = file.read()

print(content)

```

Run `main.py`, and the output is:

```text
First line
Second line
Third line
```

### Reading a file line by line

**sample.txt**

```text
First line
Second line
Third line
```

**main.py**

```python
with open('sample.txt', 'r') as file:
    for line in file:
        print(f"Line: {line.strip()}")

```

Run `main.py`, and the output is:

```text
Line: First line
Line: Second line
Line: Third line
```

### Reading all lines into a list

`for line in file` takes lines one at a time and never holds the whole file in memory. That's the default choice, especially for large files. But sometimes you need all the lines at once: to count them, to reach the fifth one, to sort them. That's what `readlines()` is for; it returns an ordinary list:

**sample.txt**

```text
First line
Second line
Third line
```

**main.py**

```python
with open('sample.txt', 'r') as file:
    lines = file.readlines()

print(lines)

```

Run `main.py`, and the output is:

```text
['First line\n', 'Second line\n', 'Third line']
```

Notice the `\n` at the end of each element: `readlines()` cuts the file at the line breaks but doesn't throw them away. That's why `line.strip()` showed up in the example above: `print` adds its own line break on top of the one that came from the file, and without `strip()` you'd get blank lines in between.

## Writing to a File

There are several ways to write data to a file:

### Writing a string

```python
with open('output.txt', 'w') as file:
    file.write("Hello, world!\n")
    file.write("Python is a great programming language.")

# Let's check what was written
with open('output.txt', 'r') as file:
    content = file.read()
    print("File contents after writing:")
<output>
File contents after writing:
</output>
    print(content)
<output>
Hello, world!
Python is a great programming language.
</output>
```

### Writing multiple lines

```python
# Writing a list of strings to a file
lines = ["First line", "Second line", "Third line"]

with open('lines.txt', 'w') as file:
    for line in lines:
        file.write(line + '\n')

# Let's check what was written
with open('lines.txt', 'r') as file:
    content = file.read()
    print("Contents of lines.txt:")
<output>
Contents of lines.txt:
</output>
    print(content)
<output>
First line
Second line
Third line

</output>
```

### Appending data to the end of a file

Next to the program sits `output.txt` with two lines — the `'a'` mode will append to its end without erasing anything:

**output.txt**

```text
Hello, world!
Python is a great programming language.
```

**main.py**

```python
with open('output.txt', 'a') as file:
    file.write("
This line was added later.")

with open('output.txt', 'r') as file:
    print(file.read())

```

Run `main.py`, and the output is:

```text
Hello, world!
Python is a great programming language.
This line was added later.
```

## Exception Handling When Working with Files

Various errors can occur when working with files: the file isn't there, you don't have permission to access it, the disk is full.

Catching such errors and reacting to them is the job of the `try/except` construct (it has its own lesson later in the course). Here it's enough to see it in action on files: the `try` block attempts the operation, and `except` catches a specific error if it happened.

```python
# Handling possible errors when opening a file
try:
    with open('non_existent_file.txt', 'r') as file:
        content = file.read()
except FileNotFoundError:
    print("Error: File not found!")
<output>
Error: File not found!
</output>
```

## Working with File Paths

So far we've written file names plainly: `open('notes.txt')`. Python looks for such a file in the current directory, the one the program was started from.

If the file sits in a neighbouring folder, you need a path. Gluing one out of strings is risky: on macOS and Linux the parts are separated by `/`, on Windows by `\`. A path written by hand breaks as soon as the code moves to another system.

So paths are assembled with the `pathlib` module from the standard library.

In `pathlib` a path is an object rather than a string: parts are joined with the `/` operator, and the name, the extension and the existence check live right on the object. Such code reads closer to how a path actually looks.

In the sandbox the current directory is `/home/pyodide`; on your own computer the path will differ.

```python
from pathlib import Path

# Current directory
current_path = Path.cwd()
print(f"Current directory: {current_path}")
<output>
Current directory: /home/pyodide
</output>

# Creating a path
data_file = current_path / 'data' / 'info.txt'
print(f"Path to file: {data_file}")
<output>
Path to file: /home/pyodide/data/info.txt
</output>

# Getting the filename and extension
document_path = Path("path/to/document.pdf")
print(f"Filename: {document_path.stem}, extension: {document_path.suffix}")
<output>
Filename: document, extension: .pdf
</output>
```

Checking existence is the `exists()` method: it answers whether a real file is at that path. Below, `sample.txt` sits next to the program and `missing.txt` does not:

**sample.txt**

```text
First line
Second line
Third line
```

**main.py**

```python
from pathlib import Path

print(Path('sample.txt').exists())
print(Path('missing.txt').exists())

```

Run `main.py`, and the output is:

```text
True
False
```

## Understanding Check

**What happens to the contents if you open an existing file in `'w'` mode?**

1. **Correct answer:** The old contents are wiped and the file starts from scratch — The "w" mode clears the file silently, with no warning, and it does so the moment the file is opened — before the first write(). To append to the end you need the "a" mode.

2. The new data is appended, the old data stays — That is how the "a" mode behaves. "w" starts the file with a clean slate.

3. Python refuses to open the file because it already exists — An existing file is opened by "w" without complaint. It is the "r" mode that complains the other way round: if the file is missing, it raises FileNotFoundError.

4. Nothing changes until write() is called — The file is cleared at the moment it is opened. Even if you close it right away without writing a single line, the previous contents are gone.

In future lessons, we'll delve deeper into working with specific file types, such as text files, CSV, JSON, and others.
