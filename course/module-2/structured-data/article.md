# JSON and CSV Formats in Python: Reading, Writing, and Data Analysis

We've already covered the basics of working with files and text files in Python.
Now we're moving on to structured data — formats that are used to store and exchange data in a structured way. 🧩

We'll focus on two of the most popular formats:

-   **JSON** (JavaScript Object Notation) — a data exchange format widely used in web applications and APIs
-   **CSV** (Comma-Separated Values) — a simple format for representing tabular data

These formats are very common and are used in all areas of programming — from web development to data analysis.

## JSON: JavaScript Object Notation

> JSON (JavaScript Object Notation) is a text-based data exchange format, similar to dictionaries and lists in Python. It is easy to read by both humans and machines.

### JSON Data Structure

JSON supports the following data types:

-   Objects (dictionaries): `{"name": "Alice", "age": 30}`
-   Arrays (lists): `[1, 2, 3, 4]`
-   Strings: `"Hello, world!"`
-   Numbers: `42`, `3.14`
-   Boolean values: `true`, `false`
-   `null` (corresponds to `None` in Python)

### The json Module in Python

Python provides a built-in `json` module for working with this format:

```python
import json

# Simple Python dictionary
person = {
    "name": "Anna",
    "age": 28,
    "city": "Moscow",
    "languages": ["Python", "JavaScript"]
}

# Converting a Python dictionary to a JSON string
json_string = json.dumps(person, ensure_ascii=False, indent=2)
print("JSON string:")
print(json_string)

# Converting a JSON string back to a Python object
parsed_data = json.loads(json_string)
print("\nConverted back to Python:")
print(f"Name: {parsed_data['name']}")
print(f"Age: {parsed_data['age']}")
print(f"Programming languages: {', '.join(parsed_data['languages'])}")
```

### Writing JSON to a File and Reading from a File

Here's how to save data to a JSON file and then read it:

```python
import json

# Student data
students = [
    {"id": 1, "name": "Ivan", "scores": [85, 90, 78]},
    {"id": 2, "name": "Maria", "scores": [92, 88, 95]}
]

# Writing to a file
with open('students.json', 'w', encoding='utf-8') as file:
    json.dump(students, file, ensure_ascii=False, indent=2)
    print("Data written to students.json file")

# Reading from a file
with open('students.json', 'r', encoding='utf-8') as file:
    loaded_students = json.load(file)
    print(f"\nLoaded {len(loaded_students)} students:")

    for student in loaded_students:
        avg_score = sum(student['scores']) / len(student['scores'])
        print(f"  {student['name']}: average score {avg_score:.1f}")
```

### Main Methods of the json Module

| Method                 | Description                                 |
| ---------------------- | ------------------------------------------- |
| `json.dumps(obj)`      | Converts a Python object to a JSON string   |
| `json.loads(str)`      | Converts a JSON string to a Python object   |
| `json.dump(obj, file)` | Writes a Python object to a JSON file       |
| `json.load(file)`      | Reads JSON from a file into a Python object |

The `ensure_ascii=False` parameter allows correct saving of non-ASCII characters and other Unicode symbols, and `indent` makes the output more readable.

## CSV: Comma-Separated Values

> CSV (Comma-Separated Values) is a simple text format for representing tabular data, where table rows are file lines, and columns are separated by commas (or other delimiters).

CSV looks something like this:

```python
Name,Age,City
Anna,28,Moscow
Ivan,35,Saint Petersburg
```

### The csv Module in Python

Python provides a built-in `csv` module for working with this format:

```python
import csv

# Data to write
data = [
    ['Name', 'Age', 'City'],  # Headers
    ['Anna', '28', 'Moscow'],
    ['Ivan', '35', 'Saint Petersburg'],
    ['Maria', '22', 'Kazan']
]

# Writing to a CSV file
with open('people.csv', 'w', newline='', encoding='utf-8') as file:
    writer = csv.writer(file)
    writer.writerows(data)
    print("Data written to people.csv file")

# Reading from a CSV file
with open('people.csv', 'r', encoding='utf-8') as file:
    reader = csv.reader(file)

    # Reading headers (first line)
    headers = next(reader)
    print(f"\nHeaders: {headers}")

    # Reading data
    print("\nData:")
    for row in reader:
        print(f"  {row[0]}, {row[1]} years old, city {row[2]}")
```

### Using DictReader and DictWriter

For more convenient work with CSV, you can use `DictReader` and `DictWriter`,
which allow you to work with data as dictionaries:

```python
import csv

# Writing to CSV using DictWriter
data = [
    {'Name': 'Alex', 'Profession': 'Engineer', 'Salary': 85000},
    {'Name': 'Kate', 'Profession': 'Designer', 'Salary': 75000},
    {'Name': 'Sergey', 'Profession': 'Programmer', 'Salary': 110000}
]

with open('employees.csv', 'w', newline='', encoding='utf-8') as file:
    fieldnames = ['Name', 'Profession', 'Salary']
    writer = csv.DictWriter(file, fieldnames=fieldnames)

    writer.writeheader()  # Writing headers
    writer.writerows(data)  # Writing data
    print("Employee data written to file")

# Reading from CSV using DictReader
with open('employees.csv', 'r', encoding='utf-8') as file:
    reader = csv.DictReader(file)

    print("\nEmployees:")
    for row in reader:
        print(f"  {row['Name']} - {row['Profession']}, salary: {row['Salary']} units")
```

### Main Features of Working with CSV

1. **Delimiters**: Although CSV stands for "Comma-Separated Values", in practice other delimiters (semicolon, tab) can be used
2. **Quotes**: If a value contains a delimiter or quotes, it is enclosed in quotes
3. **Escaping**: If there are quotes inside a value, they are escaped

```python
import csv

# Example with a different delimiter
data = [
    ['Product', 'Price', 'In Stock'],
    ['Laptop', '45000', 'Yes'],
    ['Smartphone', '25000', 'No']
]

# Writing using semicolon
with open('products.csv', 'w', newline='', encoding='utf-8') as file:
    writer = csv.writer(file, delimiter=';')
    writer.writerows(data)
    print("Data written with delimiter ';'")

# Reading with the correct delimiter
with open('products.csv', 'r', encoding='utf-8') as file:
    reader = csv.reader(file, delimiter=';')
    for row in reader:
        print('  '.join(row))
```

## Practical Example: Sales Data Analysis

Let's consider an example where we first save sales data in CSV, then analyze it and save the results in JSON:

```python
import csv
import json

# Creating sales data
sales = [
    ['Date', 'Product', 'Category', 'Price', 'Quantity'],
    ['2023-01-05', 'HP Laptop', 'Electronics', '45000', '2'],
    ['2023-01-10', 'Apple Smartphone', 'Electronics', '85000', '3'],
    ['2023-01-15', 'Book "Python"', 'Books', '1200', '5'],
    ['2023-02-10', 'Microwave', 'Home Appliances', '7000', '1']
]

# Step 1: Save data to CSV
with open('sales.csv', 'w', newline='', encoding='utf-8') as file:
    writer = csv.writer(file)
    writer.writerows(sales)
    print("Sales data saved to CSV")

# Step 2: Read and analyze data
with open('sales.csv', 'r', encoding='utf-8') as file:
    reader = csv.reader(file)
    headers = next(reader)  # Skip headers

    # Preparing variables for analysis
    total_revenue = 0
    sales_by_category = {}

    # Data analysis
    for row in reader:
        date, product, category, price, quantity = row
        revenue = float(price) * int(quantity)

        # Total revenue
        total_revenue += revenue

        # Revenue by category
        if category in sales_by_category:
            sales_by_category[category] += revenue
        else:
            sales_by_category[category] = revenue

    # Output analysis results
    print(f"\nTotal revenue: {total_revenue} units")
    print("\nRevenue by category:")
    for category, rev in sales_by_category.items():
        print(f"  {category}: {rev} units")

# Step 3: Save analysis results to JSON
results = {
    "total_revenue": total_revenue,
    "sales_by_category": sales_by_category
}

with open('sales_analysis.json', 'w', encoding='utf-8') as file:
    json.dump(results, file, ensure_ascii=False, indent=2)
    print("\nAnalysis results saved to JSON")

# Step 4: Check saved JSON
with open('sales_analysis.json', 'r', encoding='utf-8') as file:
    saved_results = json.load(file)
    print("\nContents of the JSON file with results:")
    print(json.dumps(saved_results, ensure_ascii=False, indent=2))
```

In this example we:

1. Created a CSV file with sales data
2. Read the data and calculated revenue by category
3. Saved the analysis results to a JSON file
4. Read the saved JSON to ensure correctness

## Understanding Check

**Which code correctly reads data from a JSON file in Python?**
