# SQLite in Python: relational database basics

Most production apps store data in **relational databases** — PostgreSQL, MySQL, SQLite — not in files. Sooner or later you'll write SQL queries from Python and read results back into your code.

In this article we'll walk through the basic pattern using **SQLite**: a full-featured relational database built into Python. No installation needed, the database lives in a single file, and the same approach carries over to any other RDBMS.

This article gives a **general overview** of working with SQL from Python. For a deep dive into SQL itself, see the [free SQL Academy course](https://sql-academy.org/en/guide) with practical exercises.

## Connection and cursor

Python's standard library includes the `sqlite3` module. Basic pattern: open a connection, get a `cursor` (which runs queries), close the connection.

```python-executable
import sqlite3

# Connect to the database (the file is created automatically)
connection = sqlite3.connect('tasks.db')
cursor = connection.cursor()

# ... queries go here

connection.close()
print("Done")
# Output: Done
```

It's nicer to use the connection as a context manager (`with`) — it closes itself and commits changes on exit:

```python-executable
import sqlite3

with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    # ... queries

print("Done")
# Output: Done
```

## Creating a table

In a relational database, data lives in **tables**. Each table has a schema: which columns, what types, what constraints. You create one with the SQL `CREATE TABLE` command:

```python-executable
import sqlite3

with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            completed BOOLEAN DEFAULT FALSE
        )
    ''')

print("Table tasks ready")
# Output: Table tasks ready
```

What the SQL parts mean:

-   `CREATE TABLE IF NOT EXISTS tasks` — create table `tasks` if it doesn't exist yet
-   `id INTEGER PRIMARY KEY AUTOINCREMENT` — integer primary key, auto-incremented
-   `title TEXT NOT NULL` — text field, required
-   `completed BOOLEAN DEFAULT FALSE` — boolean field, defaults to `False`

## CRUD: the four basic operations

CRUD stands for **C**reate / **R**ead / **U**pdate / **D**elete: the four operations that cover almost all data work. Going forward we'll assume `tasks` table is already created.

### CREATE: inserting data

```python-executable
import sqlite3

with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute(
        "INSERT INTO tasks (title) VALUES (?)",
        ("Learn SQLite",)
    )
    cursor.execute(
        "INSERT INTO tasks (title) VALUES (?)",
        ("Buy groceries",)
    )

print("Tasks added")
# Output: Tasks added
```

A crucial point: values are **never embedded directly in the SQL string** via f-strings or concatenation. Instead, use the `?` placeholder and pass the value as the second argument to `execute`. This guards against **SQL injection**:

```python
# UNSAFE: user input concatenated into SQL
user_input = "'; DROP TABLE tasks; --"
cursor.execute(f"INSERT INTO tasks (title) VALUES ('{user_input}')")
# becomes: INSERT ... VALUES (''); DROP TABLE tasks; --')

# SAFE: value passed separately
cursor.execute("INSERT INTO tasks (title) VALUES (?)", (user_input,))
# becomes: INSERT ... VALUES ('\'; DROP TABLE tasks; --')
```

Rule: **never concatenate user input into a SQL string**, always pass it through `?` parameters.

### READ: querying data

```python-executable
import sqlite3

with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute("SELECT id, title, completed FROM tasks")
    rows = cursor.fetchall()

for row in rows:
    print(row)
# Output:
# (1, 'Learn SQLite', 0)
# (2, 'Buy groceries', 0)
```

`cursor.fetchall()` returns all rows as a list of tuples. Access fields by index: `row[0]` is `id`, `row[1]` is `title`, etc.

For a single row (say by id), use `fetchone()`:

```python-executable
import sqlite3

with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute("SELECT title FROM tasks WHERE id = ?", (1,))
    row = cursor.fetchone()

print(row)
# Output: ('Learn SQLite',)
```

### UPDATE: changing data

```python-executable
import sqlite3

with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute(
        "UPDATE tasks SET completed = ? WHERE id = ?",
        (True, 1)
    )

print("Task 1 marked completed")
# Output: Task 1 marked completed
```

`WHERE id = ?` is essential: without a condition, `UPDATE` updates **every** row in the table.

### DELETE: removing data

```python-executable
import sqlite3

with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute("DELETE FROM tasks WHERE id = ?", (2,))

print("Task 2 deleted")
# Output: Task 2 deleted
```

Same warning: without a `WHERE`, `DELETE` empties **the whole table**.

## What's beyond this article

There are several production topics we're not covering in depth here but you should know they exist:

-   **Transactions** (`BEGIN`/`COMMIT`/`ROLLBACK`): a group of changes runs atomically, all or none. The `with sqlite3.connect(...)` block commits automatically on exit.
-   **Relationships** (`FOREIGN KEY`, `JOIN`): most real schemas have multiple linked tables (users and their tasks, orders and items).
-   **Indexes**: speed up lookups on frequently queried columns.

These are covered in the [SQL Academy course](https://sql-academy.org/en/guide).

## What's next?

The next article covers **SQLAlchemy Core**: a library that builds SQL queries from Python expressions instead of strings. SQL injections are handled automatically, and the same code works across PostgreSQL, MySQL, and SQLite.

---

**Why pass values to `execute()` through the `?` parameter instead of embedding them in the SQL string?**

