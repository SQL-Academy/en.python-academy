# SQLAlchemy ORM: database access through Python objects

In Core we built SQL from Python expressions: `select(tasks).where(tasks.c.id == 1)`. That's a huge step up from raw SQL strings, but the code still talks in terms of "tables + columns" rather than familiar objects.

**ORM** (Object-Relational Mapping) takes one more step: a table is described as a Python class, a row of that table is an instance of that class, and changing an object's attribute is automatically reflected in the database. The result is working with the DB in the language of regular Python objects.

## The model: a class as a table

In SQLAlchemy 2.0+ models are declared with `DeclarativeBase` and type annotations. This modern style replaces the older `declarative_base()`:

```python-executable
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

engine = create_engine('sqlite:///orm_tasks.db')
Base.metadata.create_all(engine)

print("Model Task and table tasks ready")
# Output: Model Task and table tasks ready
```

How to read this:

-   `class Task(Base)` — the model, a subclass of the base class
-   `__tablename__ = 'tasks'` — the table name in the DB
-   `id: Mapped[int] = mapped_column(primary_key=True)` — column `id`, type int, primary key
-   `title: Mapped[str]` — column `title`, type str, NOT NULL by default
-   `completed: Mapped[bool] = mapped_column(default=False)` — column `completed`, type bool, default `False`

Python types (`int`, `str`, `bool`) map automatically to SQL types (`INTEGER`, `VARCHAR`, `BOOLEAN`). No separate `Column(Integer, ...)` calls like in Core.

## Session: unit of work

The ORM runs queries through a `Session`. A session is a "unit of work": it holds loaded objects in memory, tracks changes, and flushes everything to the DB in one call.

```python-executable
from sqlalchemy.orm import Session

with Session(engine) as session:
    # ... work with objects here
    session.commit()

print("Session closed")
# Output: Session closed
```

`with Session(...)` closes the session on exit. `commit()` saves accumulated changes.

## CRUD through objects

Going forward we assume `engine` and the `Task` class are already defined.

### CREATE

```python-executable
from sqlalchemy.orm import Session

with Session(engine) as session:
    task1 = Task(title="Learn ORM")
    task2 = Task(title="Write code")
    session.add_all([task1, task2])
    session.commit()
    print(task1)
    print(task2)
# Output:
# Task(id=1, title='Learn ORM', completed=False)
# Task(id=2, title='Write code', completed=False)
```

Notice: `task1.id` is already populated after `commit()`. The DB assigned it automatically.

### READ

In modern SQLAlchemy 2.0 queries are written via `select()` + `session.execute()`. The old `session.query(...)` still works but is considered legacy.

```python-executable
from sqlalchemy import select
from sqlalchemy.orm import Session

with Session(engine) as session:
    # All rows
    tasks = session.execute(select(Task)).scalars().all()
    for task in tasks:
        print(task)
# Output:
# Task(id=1, title='Learn ORM', completed=False)
# Task(id=2, title='Write code', completed=False)
```

`.scalars()` is needed because `select(Task)` returns row-tuples (even if each tuple has one element). `.scalars()` unwraps them into `Task` instances.

Fetching a single record by primary key is simpler via `session.get`:

```python-executable
from sqlalchemy.orm import Session

with Session(engine) as session:
    task = session.get(Task, 1)
    print(task)
# Output: Task(id=1, title='Learn ORM', completed=False)
```

With a filter:

```python-executable
from sqlalchemy import select
from sqlalchemy.orm import Session

with Session(engine) as session:
    stmt = select(Task).where(Task.completed == False)
    pending = session.execute(stmt).scalars().all()
    for task in pending:
        print(task)
# Output:
# Task(id=1, title='Learn ORM', completed=False)
# Task(id=2, title='Write code', completed=False)
```

### UPDATE

The most comfortable part of ORM: change an attribute on an object and the Session figures out what to update.

```python-executable
from sqlalchemy.orm import Session

with Session(engine) as session:
    task = session.get(Task, 1)
    task.completed = True
    session.commit()
    print(task)
# Output: Task(id=1, title='Learn ORM', completed=True)
```

No explicit `UPDATE ... SET ... WHERE ...`. The Session tracks changed attributes and emits the right SQL on `commit()`.

### DELETE

```python-executable
from sqlalchemy.orm import Session

with Session(engine) as session:
    task = session.get(Task, 2)
    session.delete(task)
    session.commit()
    print("Task deleted")
# Output: Task deleted
```

## Relationships between tables

Real schemas are connected: a user has tasks, a post has comments. The ORM describes relationships with `relationship`, and accessing related records reads like accessing a regular attribute:

```python-executable
from sqlalchemy import ForeignKey
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship, Session
from sqlalchemy import create_engine
from typing import List

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = 'users'
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str]
    tasks: Mapped[List["UserTask"]] = relationship(back_populates="user")

class UserTask(Base):
    __tablename__ = 'user_tasks'
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    user: Mapped["User"] = relationship(back_populates="tasks")

engine = create_engine('sqlite:///orm_users.db')
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
# Output:
# Anna
#   Learn Python
#   Write code
```

Behind the scenes, `user.tasks` runs `SELECT ... FROM user_tasks WHERE user_id = ?`, but in code it looks like a plain attribute access. That's the central comfort of the ORM: a relational link reads as "a user has tasks".

## Comparing the three approaches

| Aspect                | sqlite3                | SQLAlchemy Core        | SQLAlchemy ORM       |
| --------------------- | ---------------------- | ---------------------- | -------------------- |
| Query                 | SQL string             | Python expression      | Python object        |
| Injection protection  | manual via `?`         | automatic              | automatic            |
| Cross-DB portability  | no                     | yes                    | yes                  |
| Relationships         | hand-written JOINs     | JOIN expressions       | `user.tasks`         |
| UPDATE                | `UPDATE ... SET ...`   | `update().values(...)` | `obj.field = ...`    |
| SQL control           | maximum                | high                   | medium               |

A good rule: ORM for typical business logic, Core for complex queries where you need control, raw SQL only when neither of the first two will do.

## What's next?

The ORM is a tool that optimizes the **typical** cases of DB work. If 95% of your queries are "load an object, change a field, save", the ORM saves a lot of time. When you hit a complex query or a performance-critical path, drop down into Core or write SQL directly. The three layers complement each other.

---

**The main advantage of ORM over Core?**

