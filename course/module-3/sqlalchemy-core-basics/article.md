# SQLAlchemy Core: Modern Approach to SQL in Python

We've learned the basics of working with SQLite through the `sqlite3` module. Now let's get acquainted with **SQLAlchemy Core** — a more powerful and convenient way to work with databases in Python.

## What is SQLAlchemy Core?

**SQLAlchemy Core** is a Python library that provides an elegant and powerful way to work with SQL databases. It acts as a "smart wrapper" over SQL, maintaining full control over queries while making code safer and more portable.

### Main Idea

Instead of writing SQL strings:

```sql
"SELECT * FROM users WHERE age > 25 AND city = 'Moscow'"
```

You write Python code:

```python
select(users_table).where(
    (users_table.c.age > 25) & (users_table.c.city == 'Moscow')
)
```

**SQLAlchemy Core automatically:**

-   **Protects from SQL injections** — parameters are escaped automatically
-   **Generates correct SQL** for different databases (PostgreSQL, MySQL, SQLite)
-   **Checks syntax** at code writing stage
-   **Provides autocompletion** in IDE

## Why SQLAlchemy Core?

**SQLAlchemy Core** solves key problems of working with SQL in Python:

### Security Out of the Box

**Problem with `sqlite3`:**

```python
# DANGEROUS! SQL injection vulnerability
user_input = "'; DROP TABLE users; --"
cursor.execute(f"SELECT * FROM users WHERE name = '{user_input}'")
```

**Solution with SQLAlchemy Core:**

```python
# SAFE! Automatic protection
select(users_table).where(users_table.c.name == user_input)
```

### Portability Between Databases

**Problem:** Different SQL syntax in different databases.

**Solution:** One code works everywhere:

```python
# Works with PostgreSQL, MySQL, SQLite identically
select(users_table).where(users_table.c.age > 25).limit(10)
```

### Development Convenience

-   **Autocompletion** — IDE suggests available fields and methods
-   **Error checking** — syntax errors are visible immediately
-   **Readability** — code is understandable without knowing SQL dialects

## Installing SQLAlchemy

```bash
pip install sqlalchemy
```

For working with different DBs you need additional drivers, but SQLite works immediately.

## Basic Concepts

### Engine — Database Connection

```python
from sqlalchemy import create_engine

# Create connection to SQLite
engine = create_engine('sqlite:///tasks.db', echo=True)

print("✅ Connection created!")

```

`echo=True` shows executed SQL queries — convenient for learning!

### MetaData and Table — Structure Description

```python
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String, Boolean

# Create objects for work
engine = create_engine('sqlite:///tasks.db')
metadata = MetaData()

# Describe table as Python object
tasks_table = Table(
    'tasks',
    metadata,
    Column('id', Integer, primary_key=True),
    Column('title', String, nullable=False),
    Column('completed', Boolean, default=False)
)

# Create table in DB
metadata.create_all(engine)

print("✅ Table created!")
```

**Advantages over pure SQL:**

-   **Readability** — table structure is clear immediately
-   **Autocompletion** — IDE suggests fields and methods
-   **Portability** — works with any DBMS

## CRUD Operations with SQLAlchemy Core

Let's examine all basic data operations in one example:

```python
from sqlalchemy import (
    create_engine, MetaData, Table, Column, Integer, String, Boolean,
    insert, select, update, delete
)

# === PREPARATION ===
# Create database connection
engine = create_engine('sqlite:///tasks.db')
metadata = MetaData()

# Describe table structure
tasks_table = Table(
    'tasks', metadata,
    Column('id', Integer, primary_key=True),
    Column('title', String, nullable=False),
    Column('completed', Boolean, default=False)
)

# Create table in database
metadata.create_all(engine)

with engine.connect() as connection:

    # === CREATE: Add new tasks ===
    insert_query = insert(tasks_table).values([
        {'title': 'Learn SQLAlchemy Core', 'completed': False},
        {'title': 'Write application', 'completed': False},
        {'title': 'Test code', 'completed': False}
    ])

    result = connection.execute(insert_query)
    print(f"✅ CREATE: Added {result.rowcount} tasks")


    # === READ: Read all tasks ===
    select_query = select(tasks_table)
    result = connection.execute(select_query)
    tasks = result.fetchall()

    print("\n📋 READ: List of all tasks:")
    for task in tasks:
        status = "✅" if task.completed else "⏳"
        print(f"  {task.id}. {status} {task.title}")


    # === UPDATE: Mark first task as completed ===
    update_query = update(tasks_table).where(
        tasks_table.c.id == 1
    ).values(completed=True)

    result = connection.execute(update_query)
    print(f"\n🔄 UPDATE: Updated {result.rowcount} tasks")


    # === READ: Check changes ===
    result = connection.execute(select_query)
    tasks = result.fetchall()

    print("\n📋 READ: Updated list:")
    for task in tasks:
        status = "✅" if task.completed else "⏳"
        print(f"  {task.id}. {status} {task.title}")


    # === DELETE: Delete task with ID=2 ===
    delete_query = delete(tasks_table).where(tasks_table.c.id == 2)
    result = connection.execute(delete_query)
    print(f"\n🗑️ DELETE: Deleted {result.rowcount} tasks")


    # === READ: Final list ===
    result = connection.execute(select_query)
    tasks = result.fetchall()

    print("\n📋 READ: Final list:")
    for task in tasks:
        status = "✅" if task.completed else "⏳"
        print(f"  {task.id}. {status} {task.title}")

    # Save changes
    connection.commit()

```

## Comparison with Pure SQL

| Aspect             | Pure SQL             | SQLAlchemy Core       |
| ------------------ | -------------------- | --------------------- |
| **Security**       | Need to remember `?` | Automatic protection  |
| **Portability**    | Tied to one DBMS     | Works everywhere      |
| **Readability**    | SQL strings          | Python objects        |
| **Autocompletion** | No                   | Available in IDE      |
| **Complexity**     | Simple to start      | Slightly more complex |

## What We've Learned?

Now you know the basics of SQLAlchemy Core:

**Concepts** — Engine, MetaData, Table  
**CRUD operations** — insert, select, update, delete  
**Advantages** — security, portability, convenience

This is a powerful foundation for working with any databases in Python!

## What's Next?

In the next article we'll learn **SQLAlchemy ORM** — an even higher-level approach where tables become Python classes and records become objects. This makes working with data maximally convenient.

**Main advantage of SQLAlchemy Core over pure SQL?**
