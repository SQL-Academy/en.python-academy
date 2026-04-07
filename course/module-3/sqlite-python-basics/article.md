# SQLite in Python: Working with Relational Databases Fundamentals

Relational databases dominate modern development (5 out of 8 positions in the top popular DBMS). Understanding their operating principles is the foundation for effective use of any data tools.

We'll study the basics using **SQLite** — a simple but powerful relational DB built into Python.

## Why Study SQL Before ORM?

Many jump straight to learning ORM (SQLAlchemy), but understanding SQL basics gives you:

-   Understanding of relational DB operating principles
-   Ability to debug and optimize queries
-   Confidence when working with any DBMS

## About SQL in This Course

We'll **briefly cover the basics** of working with SQLite to understand principles. For deep SQL study, we recommend the [free interactive SQL Academy course](https://sql-academy.org/en/guide) with practical exercises.

## First Steps with SQLite

SQLite is ideal for learning:

-   **Built into Python** — no need to install anything
-   **File-based DB** — everything stored in one file
-   **Full SQL** — standard commands work

Let's create a simple task management application:

```python
import sqlite3

# Connect to DB (file will be created automatically)
connection = sqlite3.connect('tasks.db')
cursor = connection.cursor()

print("✅ Database created!")
connection.close()
```

## Creating Table

```python
import sqlite3

with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()

    # Create table for tasks
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            completed BOOLEAN DEFAULT FALSE
        )
    ''')

    print("✅ Table created!")
```

**What the SQL means:**

-   `CREATE TABLE` — create table
-   `IF NOT EXISTS` — only if it doesn't exist yet
-   `INTEGER PRIMARY KEY` — unique number (ID)
-   `TEXT NOT NULL` — text field, required
-   `BOOLEAN DEFAULT FALSE` — boolean value, default `False`

## CRUD Operations

### CREATE — Adding Data

```python
import sqlite3

# First create table (if it doesn't exist)
with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            completed BOOLEAN DEFAULT FALSE
        )
    ''')

def add_task(title):
    with sqlite3.connect('tasks.db') as connection:
        cursor = connection.cursor()
        cursor.execute(
            "INSERT INTO tasks (title) VALUES (?)",
            (title,)
        )
        print(f"✅ Task '{title}' added!")

# Add tasks
add_task("Learn SQLite")
add_task("Go shopping")
```

**Important:** The `?` symbol protects from SQL injections — always use it for data insertion!

### READ — Reading Data

```python
import sqlite3

# Create table and add test data
with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            completed BOOLEAN DEFAULT FALSE
        )
    ''')
    # Add data for demonstration
    cursor.execute("INSERT OR IGNORE INTO tasks (id, title, completed) VALUES (1, 'Learn SQLite', FALSE)")
    cursor.execute("INSERT OR IGNORE INTO tasks (id, title, completed) VALUES (2, 'Go shopping', FALSE)")

def get_all_tasks():
    with sqlite3.connect('tasks.db') as connection:
        cursor = connection.cursor()
        cursor.execute("SELECT * FROM tasks")
        return cursor.fetchall()

def show_tasks():
    tasks = get_all_tasks()
    print("📋 Task list:")
    for task in tasks:
        status = "✅" if task[2] else "⏳"
        print(f"{status} {task[1]}")

show_tasks()
```

### UPDATE — Updating Data

```python
import sqlite3

# Create table with data
with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            completed BOOLEAN DEFAULT FALSE
        )
    ''')
    cursor.execute("INSERT OR IGNORE INTO tasks (id, title, completed) VALUES (1, 'Learn SQLite', FALSE)")
    cursor.execute("INSERT OR IGNORE INTO tasks (id, title, completed) VALUES (2, 'Go shopping', FALSE)")

def complete_task(task_id):
    with sqlite3.connect('tasks.db') as connection:
        cursor = connection.cursor()
        cursor.execute(
            "UPDATE tasks SET completed = TRUE WHERE id = ?",
            (task_id,)
        )
        print(f"✅ Task {task_id} completed!")

complete_task(1)
```

### DELETE — Deleting Data

```python
import sqlite3

# Create table with data
with sqlite3.connect('tasks.db') as connection:
    cursor = connection.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            completed BOOLEAN DEFAULT FALSE
        )
    ''')
    cursor.execute("INSERT OR IGNORE INTO tasks (id, title, completed) VALUES (1, 'Learn SQLite', FALSE)")
    cursor.execute("INSERT OR IGNORE INTO tasks (id, title, completed) VALUES (2, 'Go shopping', FALSE)")

def delete_task(task_id):
    with sqlite3.connect('tasks.db') as connection:
        cursor = connection.cursor()
        cursor.execute("DELETE FROM tasks WHERE id = ?", (task_id,))
        print(f"🗑️ Task {task_id} deleted!")

delete_task(2)
```

## What's Next?

In the next article we'll learn **SQLAlchemy Core** — a more powerful and convenient way to work
with SQL in Python that maintains control over queries and adds many useful features.
