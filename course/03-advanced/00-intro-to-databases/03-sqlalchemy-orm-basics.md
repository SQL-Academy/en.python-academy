---
meta:
    title: "SQLAlchemy ORM: Database Access through Python Objects"
    description: "Models as classes, rows as objects: DeclarativeBase and Mapped, Session, CRUD through attributes, and relationship links."
---

# SQLAlchemy ORM: database access through Python objects

In Core we built SQL from Python expressions like `select(...).where(...)`. That's a huge step up from raw SQL strings, but the code still talks in terms of "tables + columns" rather than familiar objects.

**ORM** (Object-Relational Mapping) takes one more step: a table is described as a Python class, a row of that table is an instance of that class, and changing an object's attribute is automatically reflected in the database. The result is working with the DB in the language of regular Python objects.

**Python object:** `Task(id=1, title="Learn SQLite")`

**row in tasks:** `1 | Learn SQLite`

The object's attributes match the row's columns, and the Session keeps both sides in sync

## The model: a class as a table

In SQLAlchemy 2.0+ models are declared with `DeclarativeBase` and type annotations:

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column

class Base(DeclarativeBase):
    pass

class Task(Base):
    __tablename__ = 'tasks'

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    completed: Mapped[bool] = mapped_column(default=False)

    def __repr__(self):
        return f"Task(id={self.id}, title={self.title!r}, completed={self.completed})"

engine = create_engine('sqlite:///:memory:')
Base.metadata.create_all(engine)

print("Model Task and table tasks are ready")
<output>
Model Task and table tasks are ready
</output>
```

How to read it:

- `class Task(Base)` — a model; `Base` is the shared parent of all models, through which SQLAlchemy collects the list of tables, like `MetaData` in Core;
- `__tablename__` — the table name in the DB;
- lines like `title: Mapped[str]` — columns: `Mapped[...]` marks "this is a table column", and the type in the brackets becomes the column type (`int` → INTEGER, `str` → VARCHAR, `bool` → BOOLEAN);
- `mapped_column(...)` is added only when the column has settings — a primary key or a default value;
- `__repr__` — the special method you know: how the object presents itself when printed.

That's why `Base.metadata.create_all(engine)` looks familiar: it's the same `create_all` as in the previous chapter.

## Session: the unit of work

Queries in the ORM go through a `Session` — the "unit of work": it keeps loaded objects in memory, tracks changes, and saves everything to the DB with a single command.

You open it with `with Session(engine) as session:` — that way the session closes itself, and `commit()` saves the accumulated changes.

## CRUD through objects

The same four operations for the third time — but now you write neither SQL nor expressions: you change Python objects, and the Session turns that into the right queries.

CREATE, READ and DELETE are recognizable at a glance; the real news in the ORM is change tracking and links between tables.

The database in the examples is created in memory: the `sqlite:///:memory:` connection string gives a clean database on every run.

### CREATE

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, Session

# Setup: the model and table from the beginning of the article
class Base(DeclarativeBase):
    pass

class Task(Base):
    __tablename__ = 'tasks'

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    completed: Mapped[bool] = mapped_column(default=False)

    def __repr__(self):
        return f"Task(id={self.id}, title={self.title!r}, completed={self.completed})"

engine = create_engine('sqlite:///:memory:')
Base.metadata.create_all(engine)

with Session(engine) as session:
    task1 = Task(title="Learn ORM")
    task2 = Task(title="Write code")
    session.add_all([task1, task2])
    session.commit()
    print(task1)
    print(task2)
<output>
Task(id=1, title='Learn ORM', completed=False)
Task(id=2, title='Write code', completed=False)
</output>
```

Notice: `task1.id` is already filled in after `commit()`. The database assigned it automatically.

### READ

Queries are written with the `select()` you know from Core, and `session.execute()` runs them:

```python
from sqlalchemy import create_engine, select
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, Session

# Setup: the model, the table and three tasks (one already done)
class Base(DeclarativeBase):
    pass

class Task(Base):
    __tablename__ = 'tasks'

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    completed: Mapped[bool] = mapped_column(default=False)

    def __repr__(self):
        return f"Task(id={self.id}, title={self.title!r}, completed={self.completed})"

engine = create_engine('sqlite:///:memory:')
Base.metadata.create_all(engine)

with Session(engine) as session:
    session.add_all([
        Task(title="Learn ORM"),
        Task(title="Write code"),
        Task(title="Ship the project", completed=True),
    ])
    session.commit()

with Session(engine) as session:
    # All rows
    stmt = select(Task)
    tasks = session.execute(stmt).scalars().all()
    for task in tasks:
        print(task)
<output>
Task(id=1, title='Learn ORM', completed=False)
Task(id=2, title='Write code', completed=False)
Task(id=3, title='Ship the project', completed=True)
</output>

    # One record by primary key
    task = session.get(Task, 1)
    print(task)
<output>
Task(id=1, title='Learn ORM', completed=False)
</output>

    # With a filter: pending only
    pending = session.execute(select(Task).where(Task.completed == False)).scalars().all()
    for task in pending:
        print(task)
<output>
Task(id=1, title='Learn ORM', completed=False)
Task(id=2, title='Write code', completed=False)
</output>
```

`.scalars()` is needed because `select(Task)` returns row tuples: it unpacks them into `Task` objects.

A single record by primary key is fastest through `session.get`.

The filter reads like in Core, except instead of `tasks_table.c.completed` it's the class attribute `Task.completed`: the third task is done, so it didn't make the selection.

### UPDATE and DELETE

The most convenient part of the ORM: change an object's attribute, and the Session figures out what to update. Deletion goes through `session.delete`:

```python
from sqlalchemy import create_engine, select
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, Session

# Setup: the model, the table and two tasks
class Base(DeclarativeBase):
    pass

class Task(Base):
    __tablename__ = 'tasks'

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    completed: Mapped[bool] = mapped_column(default=False)

    def __repr__(self):
        return f"Task(id={self.id}, title={self.title!r}, completed={self.completed})"

engine = create_engine('sqlite:///:memory:')
Base.metadata.create_all(engine)

with Session(engine) as session:
    session.add_all([Task(title="Learn ORM"), Task(title="Write code")])
    session.commit()

with Session(engine) as session:
    task = session.get(Task, 1)
    task.completed = True
    session.commit()

    task2 = session.get(Task, 2)
    session.delete(task2)
    session.commit()

    tasks = session.execute(select(Task)).scalars().all()
    for task in tasks:
        print(task)
<output>
Task(id=1, title='Learn ORM', completed=True)
</output>
```

No explicit `UPDATE ... SET` or `DELETE ... WHERE`: the Session tracks changed attributes and deleted objects and sends the right SQL on `commit()`. One task remains, and it's already done.

## Links between tables

Real schemas link tables together: a user has tasks, a post has comments.

The link rests on a **foreign key**: the `user_tasks` table has a `user_id` column holding the `id` of the owning user — that's how a task row knows whose it is. `ForeignKey("users.id")` tells the database that the value in this column must exist in `users.id`, otherwise the task would belong to no one.

`relationship` is the ORM's layer on top of that column: instead of manually finding all rows with the right `user_id`, you write `user.tasks`.

`back_populates` ties the two sides together so that `user.tasks` and `task.user` describe one link, not two independent ones. In code, reaching related records looks like accessing a regular attribute:

```python
from sqlalchemy import create_engine, ForeignKey
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship, Session

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = 'users'
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str]
    tasks: Mapped[list["UserTask"]] = relationship(back_populates="user")

class UserTask(Base):
    __tablename__ = 'user_tasks'
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    user: Mapped["User"] = relationship(back_populates="tasks")

engine = create_engine('sqlite:///:memory:')
Base.metadata.create_all(engine)

with Session(engine) as session:
    anna = User(name="Anna", tasks=[
        UserTask(title="Learn Python"),
        UserTask(title="Write code"),
    ])
    session.add(anna)
    session.commit()

    user = session.get(User, anna.id)
    print(user.name)
    for task in user.tasks:
        print(f"  {task.title}")
<output>
Anna
  Learn Python
  Write code
</output>
```

The name `"UserTask"` in the annotation is quoted because that class is declared further down the file: at the moment `User` is being read, Python doesn't know it yet.

Behind the scenes `user.tasks` runs a SQL query that picks the rows of `user_tasks` with the right `user_id`. But in code it looks like plain attribute access. That's the main comfort of the ORM: a relational link reads as "the user has tasks".

## Comparing the three approaches

| Aspect               | sqlite3              | SQLAlchemy Core        | SQLAlchemy ORM    |
| -------------------- | -------------------- | ---------------------- | ----------------- |
| Query                | SQL string           | Python expression      | Python object     |
| Injection protection | manual, via `?`      | automatic              | automatic         |
| Cross-DB portability | no                   | yes                    | yes               |
| Links                | manual JOINs         | JOIN expressions       | `user.tasks`      |
| UPDATE               | `UPDATE ... SET ...` | `update().values(...)` | `obj.field = ...` |
| Control over SQL     | maximum              | high                   | medium            |

A good rule:

- ORM — for typical business logic;
- Core — for complex queries where you need control;
- raw SQL — only when the first two fall short.

## Understanding check

**The main advantage of the ORM over Core?**

1. Queries run faster — The opposite: the ORM adds a small overhead compared to Core (object tracking). The advantage is convenience, not speed.

2. **Correct answer:** Working with regular Python objects instead of SQL expressions, with automatic change tracking — Change an attribute — the Session issues the right UPDATE on commit(). Links between tables read as attributes (user.tasks), not as explicit JOINs.

3. The ability to work without understanding SQL — You still need to understand SQL: the ORM runs SQL under the hood, and without knowing what it does you easily end up with extra slow queries. The advantage of the ORM is convenience, not "magic freedom from SQL".

4. The ORM validates the DB schema at runtime — That is more a feature of typed models (Mapped\[str]), and the checking happens in your IDE or linter, not at runtime. The main advantage of the ORM is working with objects instead of SQL.

The ORM is a tool that optimizes the **typical** cases of database work. If 95% of your queries are "get an object, change a field, save", the ORM saves a lot of time. When you hit a complex query or a performance bottleneck, drop down to Core or write SQL directly. The three levels complement each other.
