---
meta:
    title: "SQLAlchemy Core: SQL from Python expressions"
    description: "How to build SQL from Python expressions: Engine, Table, the insert/select/update/delete helpers, and one codebase for different DBMSs."
---

# SQLAlchemy Core: SQL from Python expressions

In the previous chapter, SQL queries lived in strings, and as long as the query is constant, a string works fine. But more often the query depends on data: say, finding a task by a title the user typed. The first thing that comes to mind is an f-string:

```python
query = f"SELECT * FROM tasks WHERE title = '{name}'"
```

It works right up to the day `name` contains an apostrophe or someone else's chunk of SQL. That convenience hides two problems with raw strings.

**First: SQL lives in a string**, and any careless interpolation of user data is a potential SQL injection. The `?` parameter saves you, but you have to remember it every single time.

**Second: every DBMS has its own SQL dialect.** If the app was written against SQLite and later moves to PostgreSQL, you'll almost certainly have to rewrite parts of the queries.

**SQLAlchemy Core** solves both: SQL is built from Python expressions, safety is on by default, and the same code runs against PostgreSQL, MySQL, SQLite. Plain `SELECT/WHERE` looks similar across the three, but as soon as you reach vendor-specific functions (dates, strings, aggregates), the syntax diverges, and Core translates your Python into the right dialect:

One expression — three dialects: Postgres and MySQL match, SQLite gets its own variant, and each has its own placeholder

There are really only four new words in Core:

- `Engine` — the connection to the database;
- `Table` — the table definition;
- `insert`/`select`/`update`/`delete` — helpers instead of SQL strings;
- `.c` — column access.

Everything else is the SQL you know from the previous chapter.

## Install

```bash
pip install sqlalchemy
```

For SQLite no extra drivers are needed. For PostgreSQL install `psycopg2-binary` separately, for MySQL `pymysql`.

## Engine: the connection

`Engine` is the object responsible for talking to the database. Created once per application, with the database address given by a connection string:

```python
from sqlalchemy import create_engine

engine = create_engine('sqlite:///core_tasks.db')
print("Engine ready")
<output>
Engine ready
</output>
```

`create_engine` has an `echo=True` parameter: with it, every executed SQL statement is printed to the console. Handy while debugging; turn it off in production.

Connection strings for other DBMS:

- `postgresql://user:pass@host:5432/dbname`
- `mysql+pymysql://user:pass@host/dbname`
- `sqlite:///file.db`

## Defining a table

In Core, table structure is described with a `Table` object — the Python equivalent of `CREATE TABLE`:

```python
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String, Boolean

engine = create_engine('sqlite:///core_tasks.db')
metadata = MetaData()

tasks_table = Table(
    'tasks',
    metadata,
    Column('id', Integer, primary_key=True),
    Column('title', String, nullable=False),
    Column('completed', Boolean, default=False),
)

# Create the table in the DB (if it doesn't exist yet)
metadata.create_all(engine)

print("Table tasks ready")
<output>
Table tasks ready
</output>
```

`nullable=False` is the `NOT NULL` from the previous chapter: the field is required. `MetaData` is the collection of all `Table` objects in the app. `metadata.create_all(engine)` creates every table in the collection that isn't already in the DB.

## The same four operations, as expressions

The same CREATE, READ, UPDATE, DELETE as in the previous chapter — only Python expressions instead of SQL strings. Core ships a helper for each operation: `insert()`, `select()`, `update()`, `delete()`.

What's new in the familiar four: a batch insert from a list, column access via `.c`, and Python operators inside `.where()`. The database in the examples is created in memory: the `sqlite:///:memory:` connection string gives a clean database on every run.

### INSERT and SELECT

```python
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String, Boolean
from sqlalchemy import insert, select

# Setup: the engine and table from the examples above
engine = create_engine('sqlite:///:memory:')
metadata = MetaData()

tasks_table = Table(
    'tasks', metadata,
    Column('id', Integer, primary_key=True),
    Column('title', String, nullable=False),
    Column('completed', Boolean, default=False),
)

metadata.create_all(engine)

with engine.connect() as connection:
    result = connection.execute(
        insert(tasks_table),
        [
            {'title': 'Learn SQLAlchemy Core'},
            {'title': 'Write an application'},
        ],
    )
    connection.commit()
    print(f"Rows added: {result.rowcount}")
<output>
Rows added: 2
</output>

    result = connection.execute(select(tasks_table))
    for row in result:
        print(row.id, row.title, row.completed)
<output>
1 Learn SQLAlchemy Core False
2 Write an application False
</output>
```

The values go into `insert` as a list of dictionaries: a batch insert in a single query, with SQLAlchemy substituting the parameters safely.

We didn't set the `completed` column, because the `default=False` from the table definition kicked in.

Rows are read by name (`row.title`), not by index as in `sqlite3`. And `completed` came back as `False`, not `0`: SQLAlchemy knows the column type (`Boolean`) and converts the value to the Python type itself.

### UPDATE and DELETE

A filter condition is added with the `.where()` method, rows are changed by the `update()` helper and removed by `delete()`:

```python
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String, Boolean
from sqlalchemy import insert, select, update, delete

# Setup: engine, table and two tasks
engine = create_engine('sqlite:///:memory:')
metadata = MetaData()

tasks_table = Table(
    'tasks', metadata,
    Column('id', Integer, primary_key=True),
    Column('title', String, nullable=False),
    Column('completed', Boolean, default=False),
)

metadata.create_all(engine)

with engine.connect() as connection:
    connection.execute(insert(tasks_table), [
        {'title': 'Learn SQLAlchemy Core'},
        {'title': 'Write an application'},
    ])
    connection.commit()

    connection.execute(
        update(tasks_table)
        .where(tasks_table.c.id == 1)
        .values(completed=True)
    )
    connection.execute(
        delete(tasks_table).where(tasks_table.c.id == 2)
    )
    connection.commit()

    result = connection.execute(select(tasks_table))
    for row in result:
        print(row.id, row.title, row.completed)
<output>
1 Learn SQLAlchemy Core True
</output>
```

`tasks_table.c.id` is "the `id` column of the `tasks` table", and `==` turns into a SQL comparison automatically; `>`, `<`, `.in_()` and `.like()` work the same way.

The final `select` confirms it: the first task is the only one left, already with `completed=True`. Both operations worked.

## Understanding check

**The main advantage of SQLAlchemy Core over raw SQL strings in `sqlite3`?**

1. Queries run faster — Performance is roughly equivalent — sometimes Core is even slightly slower due to building SQL from expressions. The main advantage is not speed.

2. **Correct answer:** SQL injection protection and portability across DBMS — The value goes to the driver separately from the query text, so you never interpolate it into a string by hand, and the same Python code works against PostgreSQL, MySQL, and SQLite — Core adapts the SQL to the chosen RDBMS.

3. You no longer need to write SQL at all — Knowing SQL is still required: Core maps directly onto SQL operations select, insert, update, delete. It's just Python syntax instead of strings.

4. Automatic schema documentation generation — Core does not generate docs. The real wins are safety and portability across databases.

The next article covers **SQLAlchemy ORM** — a layer above Core where tables become Python classes and rows become objects, and you barely think in SQL terms. Great for typical business logic; Core stays in the toolbox for cases where you need precise query control.
