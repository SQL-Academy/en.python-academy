---
meta:
    title: "Text Files in Python: Encodings and Parsing Text"
    description: "The UTF-8 encoding and what happens when encodings do not match. Parsing a configuration file line by line."
---

# Text Files: Encodings and Parsing Text

In the previous article we covered the basics of working with files. Here comes what is specific to text: encodings and parsing the contents line by line.

## Encodings

On disk a file is just a sequence of bytes. To turn them back into letters, you need to know which scheme to use for the conversion: many such schemes have been invented, and different languages historically ended up with different ones.

The modern standard is **UTF-8**: it covers every alphabet in the world at once, including emoji 😊. The rule for a beginner is simple: write and read in UTF-8, stating the encoding explicitly with the `encoding` parameter.

```python
text = "Привет, мир! Hello, world!"

with open('text_utf8.txt', 'w', encoding='utf-8') as file:
    file.write(text)

with open('text_utf8.txt', 'r', encoding='utf-8') as file:
    print(file.read())
<output>
Привет, мир! Hello, world!
</output>
```

Trouble starts when the encodings don't match: the file was written using one scheme and is read using another. Let's try to read our file as `ascii` — an old encoding that only has Latin letters, digits, and punctuation:

```python
with open('text_utf8.txt', 'w', encoding='utf-8') as file:
    file.write("Привет, мир! Hello, world!")

try:
    with open('text_utf8.txt', 'r', encoding='ascii') as file:
        print(file.read())
except UnicodeDecodeError as e:
    print(f"Decoding error: {e}")
<output>
Decoding error: 'ascii' codec can't decode byte 0xd0 in position 0: ordinal not in range(128)
</output>
```

The very first byte of a Cyrillic letter doesn't fit into `ascii`, and Python stops with a `UnicodeDecodeError`. The opposite situation looks the same: writing Cyrillic in an encoding that doesn't know it raises a `UnicodeEncodeError`.

The `try/except` construct is here only to keep the program from breaking off on the error. It has a lesson of its own later in the course, so there is no need to dig into it now.

## Parsing a Configuration File

Program settings are often kept in a text file: every line is a "key = value" pair. Below are two files side by side: `config.ini` with the settings and `main.py`, which builds a dictionary out of them.

**config.ini**

```text
theme = dark
language = en
autosave = True

```

**main.py**

```python
def read_config(filename):
    config = {}

    with open(filename, 'r', encoding='utf-8') as file:
        for line in file:
            key, value = line.split('=', 1)  # cut at the first '=' only
            config[key.strip()] = value.strip()

    return config


settings = read_config('config.ini')

print(settings)
print(f"Theme: {settings['theme']}, language: {settings['language']}")

```

Run `main.py`, and the output is:

```text
{'theme': 'dark', 'language': 'en', 'autosave': 'True'}
Theme: dark, language: en
```

## Test Your Understanding

**A file was written in UTF-8 but opened with `encoding='ascii'`. What happens?**

1. **Correct answer:** Python stops the program with a UnicodeDecodeError — Cyrillic bytes do not fit into ascii, and Python will not guess: the reading breaks off at the first byte that does not fit.

2. The file is read, but the Cyrillic turns into garbage characters — Garbage characters appear when the bytes formally fit some other encoding. Ascii has no Cyrillic bytes at all, so Python reports an error instead of handing you nonsense.

3. Python works out the real encoding on its own and reads it correctly — Python cannot detect the encoding from the contents. It uses the one you named, or the default one if you named none — which is exactly what causes errors on somebody else's file.

4. Only the beginning of the file is read, up to the first Cyrillic letter — There is no partial result: the error is raised on the attempt to decode the offending byte, and \`read()\` returns nothing at all.

In the next article we'll look at structured data formats — JSON and CSV.
