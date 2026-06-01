# SQLAlchemy Core: SQL from Python expressions

In the previous article we ran SQL queries through `sqlite3`. That works, but two problems linger.

**First: SQL lives in a string**, and any typo or careless interpolation of user data is a potential SQL injection. The `?` parameter saves you, but you have to remember it every single time.

**Second: every DBMS has its own SQL dialect.** If the app was written against SQLite and later moves to PostgreSQL, you'll almost certainly have to rewrite parts of the queries.

**SQLAlchemy Core** solves both: SQL is built from Python expressions, safety is on by default, and the same code runs against PostgreSQL, MySQL, SQLite. Plain `SELECT/WHERE` looks similar across the three, but as soon as you reach vendor-specific functions (dates, strings, aggregates) or schema, the syntax diverges, and Core translates your Python into the right dialect:

## Install

```bash
pip install sqlalchemy
```

For SQLite no extra drivers are needed. For PostgreSQL install `psycopg2-binary` separately, for MySQL `pymysql`.

## Engine: the connection

`Engine` is the object responsible for talking to the database. Created once per application:

```python-executable
from sqlalchemy import create_engine

engine = create_engine('sqlite:///tasks.db', echo=True)
print("Engine ready")
# Output: Engine ready
```

`echo=True` logs the SQL statements being executed. Handy while learning and debugging; turn it off in production.

Connection strings for other DBMS:

-   `postgresql://user:pass@host:5432/dbname`
-   `mysql+pymysql://user:pass@host/dbname`
-   `sqlite:///file.db`

## Defining a table

In Core, table structure is described with a `Table` object — the Python equivalent of `CREATE TABLE`:

```python-executable
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String, Boolean

engine = create_engine('sqlite:///tasks.db')
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
# Output: Table tasks ready
```

`MetaData` is the collection of all `Table` objects in the app. `metadata.create_all(engine)` creates every table in the collection that isn't already in the DB.

## CRUD: four operations

For each operation Core ships a helper: `insert()`, `select()`, `update()`, `delete()`. Going forward we assume `engine` and `tasks_table` are defined as above.

### INSERT

```python-executable
from sqlalchemy import insert

with engine.connect() as connection:
    result = connection.execute(
        insert(tasks_table),
        [
            {'title': 'Learn SQLAlchemy Core', 'completed': False},
            {'title': 'Write an app', 'completed': False},
            {'title': 'Test the code', 'completed': False},
        ],
    )
    connection.commit()

print(f"Rows inserted: {result.rowcount}")
# Output: Rows inserted: 3
```

Values are passed as a list of dicts — that's a batch insert in one query. SQLAlchemy substitutes parameters safely.

### SELECT

```python-executable
from sqlalchemy import select

with engine.connect() as connection:
    result = connection.execute(select(tasks_table))
    for row in result:
        print(row.id, row.title, row.completed)
# Output:
# 1 Learn SQLAlchemy Core 0
# 2 Write an app 0
# 3 Test the code 0
```

Access columns by name (`row.title`), not by index like in `sqlite3`. Filter conditions go through `.where()`:

```python-executable
from sqlalchemy import select

with engine.connect() as connection:
    result = connection.execute(
        select(tasks_table).where(tasks_table.c.id == 1)
    )
    row = result.first()
    print(row.title)
# Output: Learn SQLAlchemy Core
```

`tasks_table.c.id` reads as "the `id` column of the `tasks` table". Comparisons (`==`, `>`, `<`, `.in_()`, `.like()`) become SQL automatically.

### UPDATE

```python-executable
from sqlalchemy import update

with engine.connect() as connection:
    result = connection.execute(
        update(tasks_table)
        .where(tasks_table.c.id == 1)
        .values(completed=True)
    )
    connection.commit()

print(f"Rows updated: {result.rowcount}")
# Output: Rows updated: 1
```

### DELETE

```python-executable
from sqlalchemy import delete

with engine.connect() as connection:
    result = connection.execute(
        delete(tasks_table).where(tasks_table.c.id == 3)
    )
    connection.commit()

print(f"Rows deleted: {result.rowcount}")
# Output: Rows deleted: 1
```

## What's next?

The next article covers **SQLAlchemy ORM** — a layer above Core where tables become Python classes and rows become objects, and you barely think in SQL terms. Great for typical business logic; Core stays in the toolbox for cases where you need precise query control.

---

**The main advantage of SQLAlchemy Core over raw SQL strings in `sqlite3`?**

