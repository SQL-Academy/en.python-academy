---
meta:
    title: "SQLite: Your First Database in Python"
    description: "Connecting to SQLite from Python, creating a table, the four CRUD operations, and SQL injection protection with parameters."
---

# SQLite: Your First Database in Python

Your phone's messages, your browser's history, the settings of half the apps on your laptop — a huge share of them live in **SQLite**: a serverless relational database built right into Python. Nothing to install, the whole database is a single file, and the same approach later carries over to any other RDBMS.

SQLite is where we'll learn the basic pattern for working with a relational database: how to send a SQL query from Python and read the rows back into code.

## Connection and cursor

Python's standard library includes the `sqlite3` module. The basic pattern is three steps:

- open a **connection** — an opened database, like a file after `open()`;
- get a **cursor** — it sends queries into the database and holds the latest result;
- close the connection at the end.

```python
import sqlite3

# Connect to the database (the file is created automatically)
connection = sqlite3.connect('tasks.db')
cursor = connection.cursor()

# ... queries go here

connection.close()
print("Done")
<output>
Done
</output>
```

`Python code` → SQL query → `tasks.db` → rows as tuples → `Python code`

## Creating a table

In a relational database, data lives in **tables**. Every table is described by a schema: which columns, of which types, with which constraints. We create one with the SQL command `CREATE TABLE`.

From here on, every example wraps the connection in `with`: SQLite doesn't write changes instantly: they have to be confirmed (committed), and `with` does that itself on leaving the block, rolling back on error. It doesn't close the connection, though, but in short scripts like the ones below it closes together with the program; in long-running code call `close()`, as in the first example.

```python
import sqlite3

with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY,
            title TEXT NOT NULL,
            completed BOOLEAN DEFAULT FALSE
        )
    ''')

print("Table tasks is ready")
<output>
Table tasks is ready
</output>
```

What the SQL parts mean:

- `CREATE TABLE IF NOT EXISTS tasks` — create the `tasks` table if it doesn't exist yet
- `id INTEGER PRIMARY KEY` — an integer primary key; SQLite numbers new rows itself
- `title TEXT NOT NULL` — a text field, required
- `completed BOOLEAN DEFAULT FALSE` — a boolean field, `False` by default

## CRUD: the four basic operations

CRUD is an acronym for **C**reate / **R**ead / **U**pdate / **D**elete: four operations that cover almost all work with data.

We practice on an in-memory database: `:memory:` instead of a file name gives a clean database on every run. The first lines inside `with` are the setup: a table and two tasks.

### CREATE: adding data

```python
import sqlite3

with sqlite3.connect(':memory:') as connection:
    cursor = connection.cursor()
    cursor.execute("CREATE TABLE tasks (id INTEGER PRIMARY KEY, title TEXT)")

    cursor.execute(
        "INSERT INTO tasks (title) VALUES (?)",
        ("Learn SQLite",)
    )
    cursor.execute(
        "INSERT INTO tasks (title) VALUES (?)",
        ("Buy groceries",)
    )

print("Tasks added")
<output>
Tasks added
</output>
```

The values travel as a tuple in the second argument of `execute`, and the SQL itself holds a `?` parameter in their place. The comma in `("Learn SQLite",)` is required: it is what makes the parentheses a one-element tuple. Why values are never glued straight into the query string — we'll show right after we learn to read data.

### READ: reading data

```python
import sqlite3

with sqlite3.connect(':memory:') as connection:
    cursor = connection.cursor()
    cursor.execute("CREATE TABLE tasks (id INTEGER PRIMARY KEY, title TEXT)")
    cursor.execute("INSERT INTO tasks (title) VALUES ('Learn SQLite'), ('Buy groceries')")

    cursor.execute("SELECT id, title FROM tasks")
    rows = cursor.fetchall()

for row in rows:
    print(row)
<output>
(1, 'Learn SQLite')
(2, 'Buy groceries')
</output>
```

`cursor.fetchall()` returns all result rows as a list of tuples: fields are accessed by index, `row[0]` is `id`, `row[1]` is `title`.

When you need just one row, use `fetchone()` instead: it returns the first row of the result. We'll see it in action just below.

### SQL injection: why `?`

Now we can show what the `?` parameter is really for. Imagine a search field where the user types a task title — and an attacker types a piece of SQL:

```python
# DANGEROUS: user input is glued into the SQL
search = "' OR '1'='1"
cursor.execute(f"SELECT * FROM tasks WHERE title = '{search}'")
# The SQL becomes: SELECT * FROM tasks WHERE title = '' OR '1'='1'
# '1'='1' is always true → ALL tasks come back, not just the right one

# SAFE: the value travels separately
cursor.execute("SELECT * FROM tasks WHERE title = ?", (search,))
# looks for a task literally titled "' OR '1'='1" — nothing extra comes back
```

The asterisk in `SELECT *` means "all columns at once". The attacker put a piece of SQL into an ordinary search field and got every row of the table; the same trick bypasses password checks or deletes data. The rule: **never glue user input into a SQL string**, always pass parameters through `?`.

### UPDATE: changing data

```python
import sqlite3

with sqlite3.connect(':memory:') as connection:
    cursor = connection.cursor()
    cursor.execute("CREATE TABLE tasks (id INTEGER PRIMARY KEY, title TEXT, completed BOOLEAN DEFAULT FALSE)")
    cursor.execute("INSERT INTO tasks (title) VALUES ('Learn SQLite'), ('Buy groceries')")

    cursor.execute(
        "UPDATE tasks SET completed = ? WHERE id = ?",
        (True, 1)
    )

    cursor.execute("SELECT id, title, completed FROM tasks WHERE id = ?", (1,))
    print(cursor.fetchone())
<output>
(1, 'Learn SQLite', 1)
</output>
```

The first task is now done: SQLite stores boolean values as `0` and `1`, and `completed` flipped from zero to one. And `WHERE id = ?` is essential: without a condition, `UPDATE` changes **every** row in the table.

### DELETE: removing data

```python
import sqlite3

with sqlite3.connect(':memory:') as connection:
    cursor = connection.cursor()
    cursor.execute("CREATE TABLE tasks (id INTEGER PRIMARY KEY, title TEXT)")
    cursor.execute("INSERT INTO tasks (title) VALUES ('Learn SQLite'), ('Buy groceries')")

    cursor.execute("DELETE FROM tasks WHERE id = ?", (2,))

    cursor.execute("SELECT id, title FROM tasks")
    print(cursor.fetchall())
<output>
[(1, 'Learn SQLite')]
</output>
```

The second task is gone — one row remains. Same story as with `UPDATE`: without `WHERE`, the `DELETE` command removes **every** row in the table.

## What we left out

Real production code involves a few important topics we don't cover in detail here, but you should know they exist:

- **Transactions** (`BEGIN`/`COMMIT`/`ROLLBACK`): a group of changes runs atomically — all or nothing. `with sqlite3.connect(...)` commits automatically on leaving the block.
- **JOINs and multi-table queries**: most real schemas have several linked tables (users and their tasks, orders and products), and the data is pulled from them with a single query.
- **Indexes**: speed up searches on frequently used columns.

These topics are covered by the [SQL Academy course](https://sql-academy.org/en/guide).

## Understanding check

**Why should values go into `execute()` through the `?` parameter rather than directly into the SQL string?**

1. **Correct answer:** To protect against SQL injection — The query text and the values travel to the database through different channels: the query is parsed first, and the placeholder is a slot for a value. Whatever lands in the slot can no longer be read as SQL, so hostile input stays a plain string in a field, not a command.

2. To make the query run faster — There is a speed effect (the DB may cache the query plan), but it is a side benefit. The main reason is safety.

3. So SQLite can convert the types automatically — Type conversion happens either way. The main reason for parameterized queries is protection against SQL injection.

4. It is required by the SQL standard — The «?» placeholder is a feature of the sqlite3 driver, not of the SQL standard. Other DBMSs use other placeholders (%s, :name). The main reason to use them is safety.

In the next article we take on **SQLAlchemy Core**: a library that builds SQL queries from Python expressions instead of strings. SQL injection is prevented automatically, and the same code runs on PostgreSQL, MySQL and SQLite.
