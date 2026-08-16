---
meta:
    title: "String data types in Python"
    description: "Detailed description of string data in Python: creating strings, operations and methods for working with strings, string formatting."
---

# String data types in Python

Almost everything a program shows a person and gets back from them is text: a name in a signup form, an error message, a line from a file. In Python, text lives in strings — values of type `str`.

Strings are simply sequences of characters, and there's a lot you can do with them: join them together, slice them into pieces, search inside, insert variable values. Let's start with creating them.

## How to create strings in Python

Python offers several ways to create strings. You can use single, double, or triple quotes:

```python
# Strings in single quotes
single_quotes = 'Hello, world!'
print(single_quotes)
<output>
Hello, world!
</output>

# Strings in double quotes
double_quotes = "Python is fun"
print(double_quotes)
<output>
Python is fun
</output>

# Multi-line strings in triple quotes
multi_line = """Coffee recipe:
1. Boil water
2. Pour over the ground coffee
3. Wait 4 minutes"""
print(multi_line)
<output>
Coffee recipe:
1. Boil water
2. Pour over the ground coffee
3. Wait 4 minutes
</output>
```

The choice of quote type depends on the situation:

- Single (`'`) and double (`"`) quotes work the same way
- If the string already contains single quotes, it's easier to wrap it in double quotes, and vice versa
- Triple quotes (`"""` or `'''`) are good for multi-line text

### Escaping characters

Sometimes we need to include special characters in a string, such as quotes or a line break. Backslash sequences (`\`) handle this:

```python
# Using quotes inside strings with escaping
quote_inside = "He said: \"Hello!\""
print(quote_inside)
<output>
He said: "Hello!"
</output>

path = "C:\\Program Files\\Python"
print(path)
<output>
C:\Program Files\Python
</output>

# Common escape sequences
newline = "First line.\nSecond line."  # \n - newline
print(newline)
<output>
First line.
Second line.
</output>

tab = "Name:\tJohn"  # \t - tab
print(tab)
<output>
Name:	John
</output>
```

Useful escape sequences:

- `\n` for a newline
- `\t` for a tab
- `\\` for a backslash
- `\'` for a single quote
- `\"` for a double quote

### Raw strings

When there are many backslashes — a Windows file path, say — escaping each one gets tedious. An `r` prefix before the string turns escaping off: every character is taken literally.

```python
# A regular string would need double backslashes
raw_path = r"C:\Users\Documents\file.txt"
print(raw_path)
<output>
C:\Users\Documents\file.txt
</output>
```

## Immutability of strings

Strings in Python are **immutable**. This means that once a string is created, you can't modify it "in place": every operation that looks like a modification actually creates a new string.

An attempt to change a character by index fails with an error:

```python
language = "Python"

try:
    language[0] = "J"
except TypeError as e:
    print(f"Error: {e}")
<output>
Error: 'str' object does not support item assignment
</output>
```

To get a "modified" string, you build a new one and assign it, either to the same variable or to another:

```python
language = "Python"
language = "J" + "ython"
print(language)
<output>
Jython
</output>
```

This rule applies to all string operations: `+`, `.upper()`, `.replace()` and others all return a new string instead of changing the original.

## Basic string operations

### Concatenation

You can concatenate strings with the `+` operator to build new ones:

```python
first_name = "John"
last_name = "Doe"
full_name = first_name + " " + last_name
print(full_name)
<output>
John Doe
</output>

greeting = "Hello, " + full_name + "!"
print(greeting)
<output>
Hello, John Doe!
</output>
```

### Repetition

The `*` operator repeats a string multiple times:

```python
border = "=" * 20
print(border)
<output>
====================
</output>
```

### String length

To get the number of characters in a string, use the built-in `len()` function:

```python
word = "Python"
print(len(word))
<output>
6
</output>

empty = ""
print(len(empty))
<output>
0
</output>
```

### Accessing individual characters

Each character in a string can be accessed by its position (index). Indexing starts from 0, and negative indices count from the end:

![String "Python" indices: positive 0–5 above letters, negative -6…-1 below them. The slice word[1:4] highlights y, t, h](https://python-academy.org/static/guidePage/string-data-types/indexing-en.webp "String indices and slices")

```python
word = "Python"
first_letter = word[0]
second_letter = word[1]
last_letter = word[5]

print(f"First letter: {first_letter}")
print(f"Second letter: {second_letter}")
print(f"Last letter: {last_letter}")
<output>
First letter: P
Second letter: y
Last letter: n
</output>

# You can also use negative indices to count from the end
last_letter = word[-1]
second_last_letter = word[-2]
print(f"Last letter (from the end): {last_letter}")
print(f"Second to last letter: {second_last_letter}")
<output>
Last letter (from the end): n
Second to last letter: o
</output>
```

### Getting part of a string (slices)

Python has a powerful tool for working with strings: slices. They let you extract substrings by specifying the start and end indices:

```python
message = "Python Programming"

# Slice syntax: string[start:end:step]
# start is included, end is excluded

first_word = message[0:6]     # first 6 characters
print(f"First word: {first_word}")
<output>
First word: Python
</output>

second_word = message[7:]      # from index 7 to the end
print(f"Second word: {second_word}")
<output>
Second word: Programming
</output>

prefix = message[:6]           # from the start up to (not including) index 6
print(f"Prefix: {prefix}")
<output>
Prefix: Python
</output>

every_second = message[::2]    # every second character
print(f"Every other letter: {every_second}")
<output>
Every other letter: Pto rgamn
</output>

reversed_string = message[::-1]  # string in reverse
print(f"In reverse: {reversed_string}")
<output>
In reverse: gnimmargorP nohtyP
</output>
```

Slices are flexible:

- If you don't specify a start index, the slice begins at the start of the string
- If you don't specify an end index, the slice continues to the end of the string
- A negative step traverses the string in reverse

## Useful methods for working with strings

Python provides many built-in methods for working with strings. Let's look at the most useful ones.

### Changing the case of text

```python
text = "Hello PYTHON world"

upper_case = text.upper()
print(f"Uppercase: {upper_case}")
<output>
Uppercase: HELLO PYTHON WORLD
</output>

lower_case = text.lower()
print(f"Lowercase: {lower_case}")
<output>
Lowercase: hello python world
</output>

title_case = text.title()
print(f"Each word capitalized: {title_case}")
<output>
Each word capitalized: Hello Python World
</output>

capitalized = text.capitalize()
print(f"Only first letter capitalized: {capitalized}")
<output>
Only first letter capitalized: Hello python world
</output>
```

### Searching and replacing in strings

```python
text = "Python is a great programming language"

# Searching for a substring
position = text.find("great")
print(f"The word 'great' starts at position: {position}")
<output>
The word 'great' starts at position: 12
</output>

count = text.count("a")
print(f"The letter 'a' appears {count} times")
<output>
The letter 'a' appears 5 times
</output>

# Checking the start and end of a string
starts_with = text.startswith("Python")
print(f"The string starts with 'Python': {starts_with}")
<output>
The string starts with 'Python': True
</output>

ends_with = text.endswith("!")
print(f"The string ends with '!': {ends_with}")
<output>
The string ends with '!': False
</output>

# Checking for a substring
contains = "great" in text
print(f"The string contains 'great': {contains}")
<output>
The string contains 'great': True
</output>

# Replacing substrings
new_text = text.replace("great", "wonderful")
print(f"Text after replacement: {new_text}")
<output>
Text after replacement: Python is a wonderful programming language
</output>
```

### Splitting and joining strings

```python
# Splitting a string into a list of words
sentence = "Python is a great programming language"
words = sentence.split()
print(f"List of words: {words}")
<output>
List of words: ['Python', 'is', 'a', 'great', 'programming', 'language']
</output>

# Splitting on a specific delimiter
csv_data = "apple,banana,cherry"
fruits = csv_data.split(",")
print(f"List of fruits: {fruits}")
<output>
List of fruits: ['apple', 'banana', 'cherry']
</output>

# Joining a list into a string
words_to_join = ["Python", "is", "fun"]
joined_sentence = " ".join(words_to_join)
print(f"Joined sentence: {joined_sentence}")
<output>
Joined sentence: Python is fun
</output>

# Joining with another delimiter
path_parts = ["C:", "Users", "Username", "Documents"]
path = "\\".join(path_parts)
print(f"File path: {path}")
<output>
File path: C:\Users\Username\Documents
</output>
```

### Removing extra characters

For cleaning up text from extra spaces or other characters:

```python
text_with_spaces = "   Python   "

# Removing spaces from both ends
cleaned = text_with_spaces.strip()
print(f"Without spaces: '{cleaned}'")
<output>
Without spaces: 'Python'
</output>

left_cleaned = text_with_spaces.lstrip()
print(f"Without spaces on the left: '{left_cleaned}'")
<output>
Without spaces on the left: 'Python   '
</output>

right_cleaned = text_with_spaces.rstrip()
print(f"Without spaces on the right: '{right_cleaned}'")
<output>
Without spaces on the right: '   Python'
</output>

# Removing specific characters
text_with_dots = "...Python..."
without_dots = text_with_dots.strip('.')
print(f"Without dots: '{without_dots}'")
<output>
Without dots: 'Python'
</output>
```

### Method chaining

Every string method returns a new string (remember the immutability rule). That means you can call them one after another via `.` — the result of one is fed straight into the next. This is called **method chaining**.

For example, normalising user input: strip surrounding whitespace and lowercase it.

In three steps with intermediate variables:

```python
raw_input = "  Hello  "
without_spaces = raw_input.strip()
normalized = without_spaces.lower()
```

As a one-line chain:

```python
raw_input = "  Hello  "
normalized = raw_input.strip().lower()
print(f"'{normalized}'")
<output>
'hello'
</output>
```

Chaining works because `strip()` returns a new string, on which `lower()` is then called immediately. It's a common Python pattern: instead of a bucket of intermediate variables, one expressive chain.

## String formatting

You often need to insert variable values into text. In modern Python the standard tool for this is the **f-string**:

```python
name = "Anna"
age = 25

# Just add 'f' before the string and put variables inside {}
greeting = f"Hi, my name is {name} and I'm {age} years old."
print(greeting)
<output>
Hi, my name is Anna and I'm 25 years old.
</output>

# Any expression can go inside {}
price = 19.99
quantity = 3
total = f"Total: ${price * quantity:.2f}"
print(total)
<output>
Total: $59.97
</output>
```

Besides f-strings there are two other ways to format strings that you'll see in older code: the `"...".format(...)` method and the `"..." % (...)` operator. They aren't normally used in new code: f-strings are simpler and faster.

## Understanding check

Let's check how well you've understood working with strings:

**What will be the result of the following code?**

```python
text = "Python"
result = text[1:4]
```

1. "Pyt" — The slice \[1:4] starts at index 1 (the second character) and ends before index 4.

2. **Correct answer:** "yth" — The slice \[1:4] on the string "Python" returns the characters at indices 1, 2, and 3, that is "yth".

3. "ytho" — The slice \[1:4] extracts characters from index 1 through 3 inclusive, not including the character at index 4.

4. "Pyth" — The slice \[1:4] starts at the second character (index 1), not the first.
