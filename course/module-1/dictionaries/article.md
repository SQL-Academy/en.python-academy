# Dictionaries in Python

In this article, we'll explore dictionaries (dict) — one of the most powerful and flexible data types in Python.

## What is a Dictionary?

A dictionary in Python is a collection of key-value pairs.
You can think of a dictionary as a phone book,
where people's names (keys) are associated with their phone numbers (values).

Main properties of dictionaries:

-   **Keys must be unique** — you cannot have two identical keys in one dictionary
-   **Keys must be immutable** — you can use strings, numbers, tuples as keys, but not lists or dictionaries
-   **Values can be of any type** — numbers, strings, lists, other dictionaries, etc.
-   **Starting from Python 3.7, dictionaries preserve the order of adding elements** (previously order was not guaranteed)

## Creating Dictionaries

There are several ways to create a dictionary in Python:

### 1. Using curly braces {}

```python
# Empty dictionary
empty_dict = {}

# Dictionary with data
person = {"name": "John", "age": 30, "city": "New York"}
print(person)


# Nested dictionaries
nested = {
    "person": {"name": "Mary", "age": 25},
    "contacts": {"email": "mary@example.com", "phone": "+1234567890"}
}
print(nested["person"])
```

### 2. Using the dict() constructor

```python
# Empty dictionary
empty_dict = dict()

# From a list of tuples (key-value pairs)
pairs = [("name", "Anna"), ("age", 28), ("city", "Berlin")]
person = dict(pairs)
print(person)

# Using keyword arguments (only for string keys)
settings = dict(theme="dark", font_size=12, notifications=True)
print(settings)
```

### 3. Dictionary comprehensions

```python
# Dictionary of squares (number: square of number)
squares = {x: x**2 for x in range(1, 6)}
print(squares)
```

### 4. The fromkeys() method for creating a dictionary with identical values

```python
# Creating a dictionary with given keys and the same value for all
keys = ["name", "age", "city"]
defaults = dict.fromkeys(keys, None)
print(defaults)
```

## Accessing Dictionary Elements

Access to values in a dictionary is done through keys:

### Accessing by key

```python
person = {"name": "John", "age": 30, "city": "New York"}

# Getting a value by key
name = person["name"]
print(name)

# Safe way using the get() method
phone = person.get("phone")  # Returns None if the key doesn't exist
print(phone)

# The get() method with a default value
phone = person.get("phone", "Not specified")
print(phone)
```

### Checking if a key exists

```python
person = {"name": "John", "age": 30, "city": "New York"}

# Checking if a key exists
print("name" in person)

print("phone" not in person)
```

### Accessing nested dictionaries

```python
nested = {
    "person": {"name": "Mary", "age": 25},
    "contacts": {"email": "mary@example.com", "phone": "+1234567890"}
}

# Accessing values in nested dictionaries
name = nested["person"]["name"]
print(name)

# Safe access using get()
email = nested.get("contacts", {}).get("email")
print(email)

# If the path doesn't exist, None or a default value is returned
website = nested.get("contacts", {}).get("website", "Not specified")
print(website)
```

## Modifying Dictionaries

Dictionaries in Python are mutable, so they can be easily modified:

### Adding and changing elements

```python
# Creating a dictionary
person = {"name": "John", "age": 30}

# Adding a new key and value
person["city"] = "New York"
print(person)

# Changing the value of an existing key
person["age"] = 31
print(person)

# Using the update() method for bulk updates
person.update({"age": 32, "job": "developer", "language": "Python"})
print(person)
```

### Removing elements

```python
person = {"name": "John", "age": 30, "city": "New York", "job": "developer"}

# Removing an element by key using del
del person["job"]
print(person)

# Removing and returning an element using pop()
age = person.pop("age")
print(age)
print(person)

# pop() with a default value (doesn't raise an error if the key doesn't exist)
job = person.pop("job", "Not specified")
print(job)

# Removing and returning an arbitrary element using popitem()
# In Python 3.7+ returns the last added element
item = person.popitem()
print(item)
print(person)

# Removing all elements
person.clear()
print(person)
```

## Dictionary Methods

Python provides many useful methods for working with dictionaries:

### Getting keys, values, and key-value pairs

```python
person = {"name": "John", "age": 30, "city": "New York"}

# Getting all keys
keys = person.keys()
print(keys)

# Getting all values
values = person.values()
print(values)

# Getting all key-value pairs
items = person.items()
print(items)

# The objects dict_keys, dict_values and dict_items are dictionary views
# They dynamically reflect changes in the dictionary
person["job"] = "developer"
print(keys)

# Converting views to lists
keys_list = list(keys)
print(keys_list)
```

### Copying dictionaries

```python
import copy

# Dictionary with a nested structure
original = {"name": "John", "settings": {"theme": "dark"}}

# Shallow and deep copies
shallow_copy = original.copy()
deep_copy = copy.deepcopy(original)

# Changing a simple key works the same in both cases
shallow_copy["name"] = "Peter"
deep_copy["name"] = "Alice"
print(f"original['name']: {original['name']}")  # Remains John

# Differences appear when changing nested structures
shallow_copy["settings"]["theme"] = "light"
print(f"After shallow copy - original['settings']['theme']: {original['settings']['theme']}")  # Changes!

# Create a new original to demonstrate deep copy
original = {"name": "John", "settings": {"theme": "dark"}}
deep_copy = copy.deepcopy(original)
deep_copy["settings"]["theme"] = "light"
print(f"After deep copy - original['settings']['theme']: {original['settings']['theme']}")  # Doesn't change
```

### Merging dictionaries

```python
# Merging dictionaries with update
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 3, "c": 4}

# Create a copy of dict1 and update it with data from dict2
merged = dict1.copy()
merged.update(dict2)
print(merged)

# In Python 3.9+ you can use the | operator
# merged = dict1 | dict2
```

### Setting a default value

```python
# Counting word frequency
counts = {}
text = "one two one two three"
words = text.split()

# Method 1: checking if the key exists
for word in words:
    if word in counts:
        counts[word] += 1
    else:
        counts[word] = 1
print(counts)

# Method 2: using get()
counts = {}
for word in words:
    counts[word] = counts.get(word, 0) + 1
print(counts)
```

## Iterating Through Dictionaries

There are several ways to iterate through dictionary elements:

```python
person = {"name": "John", "age": 30, "city": "New York"}

# Iterating through keys (default method)
print("Iterating through keys:")
for key in person:
    print(key, ":", person[key])

# Iterating through values
print("Iterating through values:")
for value in person.values():
    print(value)

# Iterating through key-value pairs
print("Iterating through key-value pairs:")
for key, value in person.items():
    print(key, ":", value)
```
