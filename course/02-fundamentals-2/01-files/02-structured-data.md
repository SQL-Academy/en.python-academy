---
meta:
    title: "JSON and CSV Formats in Python: Reading and Writing Data"
    description: "Guide to working with structured data in Python: the json and csv modules — reading and writing data."
---

# JSON and CSV Formats in Python: Reading and Writing Data

In the previous chapter we parsed a settings file by hand, cutting the lines on the `=` sign. For anything more complex there is no need: ready-made formats exist, and Python reads and writes them with ready-made modules.

Let's cover the two most common ones:

- **JSON** (JavaScript Object Notation) — a data-exchange format used on the web and in APIs
- **CSV** (Comma-Separated Values) — a table format; opens in Excel and Google Sheets

## JSON: JavaScript Object Notation

### What JSON Can Hold

JSON was designed as an exchange format between different languages, so its set of types is deliberately modest, limited to what exists everywhere:

| JSON    | Python      |
| ------- | ----------- |
| object  | dict        |
| string  | str         |
| number  | int / float |
| boolean | bool        |
| null    | None        |
| array   | list        |

The correspondence is almost one to one: only a number has two Python counterparts: a whole one becomes an `int`, a fractional one a `float`. That's what makes JSON so comfortable to use from Python.

### The json Module

The standard library ships a `json` module for the format, and all the work with it boils down to two actions: turn a Python object into JSON, and parse JSON back into an object.

The module has four functions. `dump` and `load` work with a file: the first writes a Python object, the second reads it back. `dumps` and `loads` do the same with a string: the `s` at the end stands for **string**.

`JSON string` ⇄ `json.loads` / `json.dumps` ⇄ `Python object` ⇄ `json.load` / `json.dump` ⇄ `JSON file`

Let's start with strings: that way the format itself is visible. `dumps` takes two arguments worth passing almost every time. `ensure_ascii=False` keeps non-ASCII characters as they are; without it a city like `Zürich` ends up in the resulting string as `Z\u00fcrich`: technically correct, humanly unreadable. And `indent=2` adds line breaks and indentation so the result can be read with your eyes.

```python
import json

person = {
    "name": "Anna",
    "age": 28,
    "city": "Zürich",
    "languages": ["Python", "JavaScript"]
}

json_string = json.dumps(person, ensure_ascii=False, indent=2)
print(json_string)
<output>
{
  "name": "Anna",
  "age": 28,
  "city": "Zürich",
  "languages": [
    "Python",
    "JavaScript"
  ]
}
</output>

parsed_data = json.loads(json_string)
print(f"Name: {parsed_data['name']}, languages: {parsed_data['languages']}")
<output>
Name: Anna, languages: ['Python', 'JavaScript']
</output>
```

### Writing JSON to a File and Reading from a File

Usually the data needs more than becoming a string: it needs to land on disk. That's where the pair without the `s` comes in: `dump` writes the object straight into an open file, `load` reads it back, no intermediate string required.

```python
import json

students = [
    {"id": 1, "name": "Ivan", "scores": [85, 90, 78]},
    {"id": 2, "name": "Maria", "scores": [92, 88, 95]}
]

with open('students.json', 'w', encoding='utf-8') as file:
    json.dump(students, file, ensure_ascii=False, indent=2)

with open('students.json', 'r', encoding='utf-8') as file:
    loaded_students = json.load(file)

for student in loaded_students:
    print(f"  {student['name']}: scores {student['scores']}")
<output>
  Ivan: scores [85, 90, 78]
  Maria: scores [92, 88, 95]
</output>
```

## CSV: Comma-Separated Values

### The csv Module

Below are two files: `people.csv` with a small table and `main.py`, which reads it. Start with the `people.csv` tab. That's the core of the format: the first line holds the headers, every following line is a table row, and the columns are separated by commas.

Looking at a file like that, it's easy to decide no module is needed: split the line on commas with `split(",")`, and you're done. And that works, but only up to the first comma inside a value: sooner or later one turns up in an address or a product description. The `csv` module knows the rules of the format and takes that trouble off your hands. In `main.py` the file is read by `csv.reader`:

**people.csv**

```csv
Name,Age,City
Anna,28,Moscow
Ivan,35,Saint Petersburg
Maria,22,Kazan

```

**main.py**

```python
import csv

with open('people.csv', 'r', encoding='utf-8') as file:
    reader = csv.reader(file)
    next(reader)  # skipping the header line

    for row in reader:
        print(f"  {row[0]}, {row[1]} years old, city {row[2]}")

```

Run `main.py`, and the output is:

```text
  Anna, 28 years old, city Moscow
  Ivan, 35 years old, city Saint Petersburg
  Maria, 22 years old, city Kazan
```

### A Comma Inside a Value

Back to the problem we promised. What happens when a comma lands inside a value itself, say in a description like "light, thin"? A naive `split(",")` would cut that value in two: one column would turn into two, and the whole row would slide to the right.

The format solves this with quotes: a value containing a comma is wrapped in quotes, and only commas outside them count as delimiters. You don't place those quotes by hand: writing a table to a file is the job of `csv.writer`, and it adds the quotes itself, while `csv.reader` strips them on the way back.

The file for writing is opened with `newline=''`: that lets the `csv` module manage the line breaks itself; otherwise blank lines would appear between the table rows on Windows.

Let's see what the file looks like:

```python
import csv

rows = [
    ['Product', 'Description', 'Price'],
    ['Laptop', 'light, thin', '45000'],
]

with open('quoted.csv', 'w', newline='', encoding='utf-8') as file:
    csv.writer(file).writerows(rows)

# How it looks in the file
with open('quoted.csv', 'r', encoding='utf-8') as file:
    print(file.read(), end="")
<output>
Product,Description,Price
Laptop,"light, thin",45000
</output>
```

The quotes appeared in the file and disappeared in Python, and there are still three columns, exactly as before.

Quotes are not the format's only subtlety: despite the name, the delimiter is not always a comma. In exports from localised versions of Excel it is usually `;`, because the comma is taken there as the decimal separator. You set the delimiter with the `delimiter` parameter, and it has to be the same when writing and when reading:

```python
writer = csv.writer(file, delimiter=';')
reader = csv.reader(file, delimiter=';')
```

Otherwise the whole line comes back as a single chunk instead of separate columns.

### Addressing Columns by Name

Up to now rows have arrived as lists, and we reached the value we wanted by number: `row[0]`, `row[1]`, `row[2]`. While there are three columns and the file is your own, that's tolerable. But let someone add a new column in the middle of the export, and every number shifts. The program won't crash: it will quietly start printing the city where the age was expected. A wrong answer with no error is harder to find than a crash.

`DictReader` gets rid of the numbers. It looks at the header line and hands back each row as a dictionary whose keys are the column names. Let's read the same `people.csv` with it:

**people.csv**

```csv
Name,Age,City
Anna,28,Moscow
Ivan,35,Saint Petersburg
Maria,22,Kazan

```

**main.py**

```python
import csv

with open('people.csv', 'r', encoding='utf-8') as file:
    for row in csv.DictReader(file):
        print(f"  {row['Name']}, {row['Age']} years old, city {row['City']}")

```

Run `main.py`, and the output is:

```text
  Anna, 28 years old, city Moscow
  Ivan, 35 years old, city Saint Petersburg
  Maria, 22 years old, city Kazan
```

The result is the same, but the code holds column names instead of numbers, and a new column in the file shifts nothing. For writing dictionaries there is a matching `DictWriter`: it works the same way, it just asks for the list of column names up front.

## Understanding Check

**How does `json.dumps` differ from `json.dump`?**

1. dumps works with dictionaries, while dump works with lists — The data type has nothing to do with it: both functions handle dictionaries and lists alike.

2. dumps keeps non-ASCII characters, dump replaces them with codes — Non-ASCII characters are the job of a separate argument, \`ensure_ascii=False\`, and both functions accept it.

3. **Correct answer:** dumps returns a string, while dump writes straight into an open file — The trailing \`s\` stands for string: such a function hands back a string in memory. Without the \`s\` the function takes an open file and writes the data there.

4. There is no difference, they are two names for one function — The functions are different: \`dumps\` returns a string, \`dump\` writes into an open file.
