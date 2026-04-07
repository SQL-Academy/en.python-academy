# SQLAlchemy ORM: Object-Relational Mapping in Python

We've learned SQLAlchemy Core — a powerful way to work with SQL through Python code. Now let's get acquainted with **SQLAlchemy ORM** — an even more convenient approach where database tables become Python classes and records become objects.

## What is ORM?

**ORM (Object-Relational Mapping)** is a technology that connects objects in code with records in the database. Instead of writing SQL queries, you work with regular Python objects.

### Main Idea

**Without ORM (SQLAlchemy Core):**

```python
# Create SQL query to get user
select_query = select(users_table).where(users_table.c.id == 1)
result = connection.execute(select_query)
user_row = result.fetchone()
print(user_row.name)  # Work with Row object
```

**With ORM:**

```python
# Work with Python object directly
user = session.get(User, 1)  # User is a Python class
print(user.name)  # Access object attribute
user.email = "new@email.com"  # Change like regular object
session.commit()  # Save changes
```

### ORM Advantages

-   **Pythonic code** — work with objects, not SQL strings
-   **Automatic change tracking** — ORM knows what needs to be saved
-   **Simple table relationships** — `user.posts` instead of JOIN queries
-   **Data validation** — checks at Python class level

## Why Core First, Then ORM?

**Understanding basics** — Core showed how SQL queries work under the hood  
**Control** — sometimes you need precise control over SQL  
**Debugging** — knowing Core makes it easier to understand what ORM does  
**Tool choice** — appropriate abstraction level for each task

## Installation and Setup

ORM is included in the main SQLAlchemy package:

```bash
pip install sqlalchemy
```

## Creating Models

In ORM, tables are described as Python classes. Let's start with a simple example:

### Step 1: Basic Setup

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# Base class for all models
Base = declarative_base()

# Database connection
engine = create_engine('sqlite:///orm_tasks.db')

print("✅ Basic setup ready!")

```

### Step 2: Creating Model

```python
from sqlalchemy import Column, Integer, String, Boolean

# Model = Python class = table in DB
class Task(Base):
    __tablename__ = 'tasks'  # Table name

    # Table fields as class attributes
    id = Column(Integer, primary_key=True)
    title = Column(String, nullable=False)
    completed = Column(Boolean, default=False)

    # Nice object representation
    def __repr__(self):
        status = "✅" if self.completed else "⏳"
        return f"<Task: {status} {self.title}>"

print("✅ Task model created!")

```

### Step 3: Creating Table and Session

```python
# Create table in database
Base.metadata.create_all(engine)

# Create session for working with data
Session = sessionmaker(bind=engine)
session = Session()

print("✅ Table created, session ready!")

```

## CRUD Operations Through ORM

Now let's study basic data operations. Each operation in a separate example:

### CREATE: Creating Objects

```python
# Create objects like regular Python instances
task1 = Task(title="Learn ORM")
task2 = Task(title="Write code")

# Add to session and save
session.add_all([task1, task2])
session.commit()

print("✅ CREATE: Tasks created:")
print(f"  {task1}")
print(f"  {task2}")

```

### READ: Reading Data

```python
# Get all tasks
all_tasks = session.query(Task).all()
print("📋 READ: All tasks:")
for task in all_tasks:
    print(f"  {task}")

```

```python
# Get specific task by ID
task = session.get(Task, 1)
print(f"🎯 Task with ID=1: {task}")

```

### UPDATE: Updating Objects

```python
# Just change object attribute!
task = session.get(Task, 1)
task.completed = True
task.title = "Learn ORM ✨"

# Save changes
session.commit()

print(f"🔄 UPDATE: {task}")

```

### DELETE: Deleting Objects

```python
# Find and delete task
task_to_delete = session.get(Task, 2)
session.delete(task_to_delete)
session.commit()

print(f"🗑️ DELETE: Task deleted")


# Check what's left
remaining_tasks = session.query(Task).all()
print(f"📋 Remaining tasks: {len(remaining_tasks)}")
for task in remaining_tasks:
    print(f"  {task}")

```

### Main ORM Advantages

-   **Simplicity** — work with objects as usual in Python
-   **Automation** — ORM tracks changes itself
-   **Security** — protection from SQL injections out of the box
-   **Readability** — code is understandable without SQL knowledge

## Relationships Between Tables

ORM allows easy table linking. Let's show with a simple example:

```python
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship

# User (one) → Tasks (many)
class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    name = Column(String, nullable=False)

    # Link to tasks
    tasks = relationship("UserTask")

# Task belongs to user
class UserTask(Base):
    __tablename__ = 'user_tasks'

    id = Column(Integer, primary_key=True)
    title = Column(String, nullable=False)
    user_id = Column(Integer, ForeignKey('users.id'))

# Create tables and data
Base.metadata.create_all(engine)
session = Session()

user = User(name="Anna")
task1 = UserTask(title="Learn Python", user_id=1)
task2 = UserTask(title="Write code", user_id=1)

session.add_all([user, task1, task2])
session.commit()

# ORM magic: get related objects
print(f"👤 User: {user.name}")
print(f"📋 Tasks: {len(user.tasks)}")
for task in user.tasks:
    print(f"  - {task.title}")

session.close()

```

**Main advantage:** `user.tasks` automatically gets related records without writing JOIN queries!

## Approach Comparison

| Aspect              | Pure SQL         | SQLAlchemy Core     | SQLAlchemy ORM      |
| ------------------- | ---------------- | ------------------- | ------------------- |
| **Syntax**          | SQL strings      | Python functions    | Python objects      |
| **Security**        | Manual           | Automatic           | Automatic           |
| **Table relations** | JOIN queries     | Complex expressions | `user.tasks`        |
| **Changes**         | UPDATE SQL       | `update().values()` | `obj.field = value` |
| **Learning curve**  | Need to know SQL | Medium              | Simple to start     |
| **Performance**     | Maximum          | High                | Good                |

## What We've Learned?

Now you know SQLAlchemy ORM basics:

**ORM concept** — objects instead of SQL  
**Creating models** — classes as tables  
**CRUD through objects** — intuitive operations  
**Table relationships** — simple work with relations

**Main advantage of ORM over Core?**
