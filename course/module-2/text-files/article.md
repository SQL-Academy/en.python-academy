# Text Files: Encodings, Efficient Processing, and Analysis

In the previous article, we covered the basics of working with files in Python.
Now, we will delve deeper into working with text files, which are one of the most common types of files in programming. 📝

Text files are used everywhere: for storing configurations, logs, data, program source code, and much more.
The ability to work with them effectively is an essential skill for any programmer.

## Specifics of Working with Text Files

Text files store sequences of characters organized into lines. When working with them in Python, there are several important points to consider:

### Character Encodings

> An encoding is a way of representing characters as bytes. Different encodings use different schemes to map characters.

In the modern world, there are many different languages and alphabets.
To allow computers to work with text in different languages, various character encoding systems have been developed:

-   **ASCII** — the simplest encoding, containing only Latin letters, numbers, and basic symbols (128 characters in total).
-   **UTF-8** — the modern standard that supports all languages of the world (including emoji 😊).
-   **Windows-1251** (cp1251) — an encoding for Cyrillic, popular in Windows.

In Python, it is recommended to always use UTF-8, especially if your text contains more than just English letters:

```python
# Writing text in different encodings
text = "Привет, мир! Hello, world! 你好，世界！"

# Writing in UTF-8 (standard for international texts)
with open('text_utf8.txt', 'w', encoding='utf-8') as file:
    file.write(text)

# Writing in ASCII (only English letters)
try:
    with open('text_ascii.txt', 'w', encoding='ascii') as file:
        file.write(text)
except UnicodeEncodeError as e:
    print(f"ASCII encoding error: {e}")

# Writing in cp1251 (Cyrillic for Windows)
with open('text_cp1251.txt', 'w', encoding='cp1251') as file:
    # Chinese characters will be replaced with '?'
    file.write(text)
```

As you can see, trying to write Russian or Chinese text in ASCII encoding causes an error, as ASCII only supports English characters.

### Reading with the Correct Encoding

When reading a file, it is important to specify the same encoding with which it was created:

```python
# Reading a file with a specified encoding
with open('text_utf8.txt', 'r', encoding='utf-8') as file:
    content = file.read()
    print(f"Content in UTF-8: {content}")

# Reading with the wrong encoding can lead to errors
try:
    with open('text_utf8.txt', 'r', encoding='ascii') as file:
        content = file.read()
        print(f"Content with wrong encoding: {content}")
except UnicodeDecodeError as e:
    print(f"Decoding error: {e}")
```

### Determining a File's Encoding

Sometimes you receive a text file and don't know what encoding it was saved in.
Python cannot automatically determine the encoding, but there are libraries that can help:

```python
# The chardet library for detecting encoding
import chardet

# Create a file in cp1251
with open('text_cp1251.txt', 'w', encoding='cp1251') as file:
    file.write("Привет, мир!")

# Read the file as bytes and determine the encoding
with open('text_cp1251.txt', 'rb') as file:
    raw_data = file.read()
    result = chardet.detect(raw_data)
    print(f"Detected encoding: {result}")

    # Now we can open the file with the correct encoding
    encoding = result['encoding']
    with open('text_cp1251.txt', 'r', encoding=encoding) as text_file:
        content = text_file.read()
        print(f"Correctly read content: {content}")
```

## Efficiently Reading Large Text Files

When working with large text files, it's important to use methods that don't load the entire file into memory.
This is especially critical when you are working with files that are hundreds of megabytes or gigabytes in size.

### Why You Shouldn't Read the Entire File at Once

When you use the `read()` method without arguments, Python loads the entire file into memory:

```python
with open('big_file.txt', 'r') as file:
    content = file.read()  # The entire file is loaded into memory!
```

This can cause problems:

1.  **Memory Consumption** — if the file is very large (e.g., gigabytes), it can take up all available RAM, leading to a slowdown or even a program crash.
2.  **Latency** — reading the entire file at once takes time, and your program will "hang" until the reading is complete.
3.  **Inefficiency** — often, you don't need all the data at once, but rather sequential access to it.

### Line-by-Line Reading

A more efficient approach is to read the file line by line using a loop.
Python will load only one line into memory at a time:

```python
# Create a test file with a large number of lines
with open('big_file.txt', 'w') as file:
    for i in range(1000):
        file.write(f"Line number {i+1}\n")

# Efficient reading line by line
with open('big_file.txt', 'r') as file:
    line_count = 0
    for line in file:  # Iterate over lines without loading the whole file into memory
        line_count += 1
        if line_count <= 5:  # Show only the first 5 lines
            print(line.strip())
    print(f"Total lines: {line_count}")
```

The advantages of this approach are:

-   Only one line is in memory at a time.
-   Processing starts immediately; no need to wait for the entire file to load.
-   You can stop reading at any time if you find the data you need.

### Reading in Chunks

If you need even more control over the reading process, you can read the file in fixed-size chunks:

```python
# Reading a file in chunks
with open('big_file.txt', 'r') as file:
    block_size = 100  # Block size in bytes
    blocks_read = 0

    while True:
        block = file.read(block_size)
        if not block:  # If the block is empty, the end of the file has been reached
            break

        blocks_read += 1
        if blocks_read <= 2:  # Show only the first 2 blocks
            print(f"Block {blocks_read}: {block[:50]}...")  # Print the beginning of the block

    print(f"Total blocks read: {blocks_read}")
```

This method allows you to control the amount of memory used for reading the file.
The block size can be adjusted depending on your needs and available memory.

## Practical Example: Reading and Processing a Configuration File

Let's look at a simple practical example: reading a configuration file and using its parameters in a program:

```python
# Create a sample configuration file
config_text = """
# Database parameters
database_host = localhost
database_port = 5432
database_name = myapp
database_user = admin
database_password = secret123

# Web server parameters
server_port = 8080
debug_mode = True
log_level = INFO
"""

with open('config.ini', 'w') as config_file:
    config_file.write(config_text)

# Reading and processing the configuration
def read_config(filename):
    config = {}

    with open(filename, 'r') as file:
        for line in file:
            # Skip empty lines and comments
            line = line.strip()
            if not line or line.startswith('#'):
                continue

            # Split key and value
            if '=' in line:
                key, value = line.split('=', 1)
                config[key.strip()] = value.strip()

    return config

# Read the configuration
app_config = read_config('config.ini')

# Use the parameters
print("Application Configuration:")
print(f"Database: {app_config['database_name']} on {app_config['database_host']}:{app_config['database_port']}")
print(f"DB User: {app_config['database_user']}")
print(f"Web Server Port: {app_config['server_port']}")
print(f"Debug Mode: {app_config['debug_mode']}")
```

In this example, we:

1.  Created a configuration file with parameters.
2.  Wrote a function to read and process the file line by line.
3.  Extracted the necessary parameters and used them in the program.

This approach is often used in real applications to store settings in a human-readable format.

## Test Your Understanding

Let's check how well you've understood the topic of reading and writing text files:

**How do you correctly open a file for reading with UTF-8 encoding?**


Now you know the basic principles of working with text files in Python.
In the next article, we will look at working with structured data formats like JSON and CSV. See you there! 👋
