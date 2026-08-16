---
meta:
    title: "Dictionaries in Python"
    description: "Dictionaries in Python: why they beat parallel lists, access via square brackets and get, adding and removing keys, iteration, and counting frequencies."
---

# Dictionaries in Python

Say you're storing students' grades. The first idea is two lists, names and grades, with matching positions linking them.

```python
names = ["John", "Mary", "Kate"]
grades = [90, 75, 80]
```

To find Mary's grade you first have to locate her position in `names`, then read the same position in `grades`. Add a student and you must remember to append to the second list too. Let one list drift by a single element and Mary gets someone else's grade, and the program won't say a word. The link between a name and a grade lives only in your head, not in the code.

A dictionary removes the index middleman and ties the name straight to its value.

```python
grades = {"John": 90, "Mary": 75, "Kate": 80}
print(grades["Mary"])
<output>
75
</output>
```

> A dictionary (dict) is a collection of key-value pairs. You look up a value by its key, not by a numeric position.

A phone book works the same way: you find a person by name, not by line number.

## Creating a dictionary

Most often a dictionary is written directly, in curly braces: a `key: value` pair, pairs separated by commas.

```python
# An empty dictionary to fill later
prices = {}

# A dictionary with data right away
person = {"name": "John", "age": 30, "city": "New York"}
print(person)
<output>
{'name': 'John', 'age': 30, 'city': 'New York'}
</output>
```

If the pairs already exist somewhere, for instance arriving as pairs, you can build a dictionary with the `dict()` function.

```python
pairs = [("name", "Anna"), ("age", 28), ("city", "Berlin")]
person = dict(pairs)
print(person)
<output>
{'name': 'Anna', 'age': 28, 'city': 'Berlin'}
</output>
```

## Reading a value: brackets vs get

The most direct way to get a value is square brackets with the key.

```python
person = {"name": "John", "age": 30}
print(person["name"])
<output>
John
</output>
```

But if the key isn't in the dictionary, the brackets don't return emptiness, they stop the program with a `KeyError`.

```python
person = {"name": "John", "age": 30}
print(person["phone"])  # KeyError: 'phone' — there's no such key
```

This isn't a flaw but a safeguard: most often reaching for a missing key is a typo or broken logic, and it's better to hear about it immediately. Brackets fit when you're sure the key exists.

When a key might be absent, there's the `get()` method. It returns `None` instead of an error, and if you pass a second argument, that argument becomes the default value.

```python
person = {"name": "John", "age": 30}

print(person.get("phone"))
<output>
None
</output>

print(person.get("phone", "not provided"))
<output>
not provided
</output>
```

Hence a simple rule of thumb: brackets when a missing key is an error, `get()` when a missing key is a normal case you're ready to handle.

## Adding and changing

A dictionary uses the same syntax to add a key and to change an existing one: assignment by key. If the key wasn't there, it appears; if it was, the value is overwritten.

```python
person = {"name": "John", "age": 30}

# Key "city" didn't exist — it gets added
person["city"] = "New York"
print(person)
<output>
{'name': 'John', 'age': 30, 'city': 'New York'}
</output>

# Key "age" already exists — the value is replaced
person["age"] = 31
print(person)
<output>
{'name': 'John', 'age': 31, 'city': 'New York'}
</output>
```

When you need several changes at once, `update()` is handier: it adds new keys and overwrites matching ones in a single call.

```python
person = {"name": "John", "age": 31}
person.update({"age": 32, "job": "developer"})
print(person)
<output>
{'name': 'John', 'age': 32, 'job': 'developer'}
</output>
```

## Checking whether a key exists

To find out in advance whether a key is present, use the `in` operator. This is exactly how you avoid a `KeyError` when you do need the brackets.

```python
person = {"name": "John", "age": 30}

print("name" in person)
<output>
True
</output>

print("phone" in person)
<output>
False
</output>
```

## Removing

You can drop a key with the `del` operator. But if the key isn't there, `del` also fails with a `KeyError`.

```python
person = {"name": "John", "age": 30, "job": "developer"}

del person["job"]
print(person)
<output>
{'name': 'John', 'age': 30}
</output>
```

The `pop()` method removes a key and returns its value at the same time, which helps when you still need the value being removed. With a second argument it doesn't fail on a missing key but returns the default instead.

```python
person = {"name": "John", "age": 30}

age = person.pop("age")
print(age)
<output>
30
</output>

# Key "phone" doesn't exist, but the second argument saves us from an error
phone = person.pop("phone", "not provided")
print(phone)
<output>
not provided
</output>
```

## Iterating over a dictionary

You can walk through a dictionary with a loop. By default a `for` loop goes over the keys, and the value is easy to fetch by key.

```python
grades = {"John": 90, "Mary": 75, "Kate": 80}

for name in grades:
    print(name, ":", grades[name])
<output>
John : 90
Mary : 75
Kate : 80
</output>
```

If you need both the key and the value inside the loop, the `items()` method hands them over as a pair at once, with no lookup by key.

```python
grades = {"John": 90, "Mary": 75, "Kate": 80}

for name, grade in grades.items():
    print(name, ":", grade)
<output>
John : 90
Mary : 75
Kate : 80
</output>
```

There are matching methods too: `keys()` gives only the keys, `values()` only the values. They're useful when the other half of the pair isn't needed in the loop.

```python
grades = {"John": 90, "Mary": 75, "Kate": 80}

total = 0
for grade in grades.values():
    total = total + grade
print("Total points:", total)
<output>
Total points: 245
</output>
```

## A common task: counting

A dictionary is a great fit for counting something: the key is the object, the value is the counter. Let's count how many times each word appears.

The straightforward version looks like this: for each word we check whether we've seen it before, and either bump the counter or start a new one.

```python
text = "one two one two three"
words = text.split()

counts = {}
for word in words:
    if word in counts:
        counts[word] = counts[word] + 1
    else:
        counts[word] = 1
print(counts)
<output>
{'one': 2, 'two': 2, 'three': 1}
</output>
```

This is exactly where `get()` with a default helps: "take the current counter, and if the word wasn't there yet, treat it as zero." The `if/else` collapses into one line.

```python
text = "one two one two three"
words = text.split()

counts = {}
for word in words:
    counts[word] = counts.get(word, 0) + 1
print(counts)
<output>
{'one': 2, 'two': 2, 'three': 1}
</output>
```

## What can be a key

Keys live by two rules, and both follow from how a dictionary is built internally.

- **Keys are unique.** You can't write two identical keys: the second assignment simply overwrites the first. That makes sense — otherwise it would be unclear which value the key `"Mary"` should return.
- **A key must be immutable.** Strings, numbers, and tuples work. A list can't be a key: Python finds a value by the key through its immutable contents, and a list could be changed after it became a key, which would "lose" the value.

```python
# A list as a key — an error
broken = {[1, 2]: "value"}  # TypeError: unhashable type: 'list'
```

Values, on the other hand, can be anything: numbers, strings, lists, even other dictionaries.

## Check your understanding

**What does `print(person["phone"])` output if the dictionary `person` has no `"phone"` key?**

1. It returns None and the program keeps running — None on a missing key is what the get() method returns. Square brackets on a non-existent key stop the program with a KeyError.

2. **Correct answer:** The program stops with a KeyError — Access through brackets requires the key to exist. If it might be absent, person.get("phone") is safer — it returns None or a default value.

3. A "phone" key with an empty value is added to the dictionary — Reading by key never adds anything to a dictionary. A new key appears only on assignment: person\["phone"] = ....
