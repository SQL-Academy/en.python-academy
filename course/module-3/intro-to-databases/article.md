---
meta:
    title: "What a Database Is and Why You Need One"
    description: "Why a Python list is not enough for storing application data, what a database and a DBMS are, and the main types of databases."
---

# What a Database Is and Why You Need One

Almost any application stores something for the long term: users, orders, messages. The first idea is to keep them in a familiar Python list. Let's see where that idea breaks down.

## Why not keep the data in a Python list

```python
users = [
    {"id": 1, "name": "Anna", "email": "anna@example.com"},
    {"id": 2, "name": "Peter", "email": "petr@example.com"},
    # ... imagine a million users here
]

def find_user_by_email(email):
    for user in users:   # scanning the whole list
        if user["email"] == email:
            return user
    return None
```

With a thousand users everything works. Then the problems begin:

- **Slow search** — "find by email" walks through the records one by one, and at a million that's a noticeable delay.
- **Everything in RAM** — the data must fit into memory entirely.
- **Data lives until restart** — the program exits, and the list is gone.
- **Concurrent access** — two people change the data at the same moment, and a list isn't ready for that.

## What a database is

These are exactly the problems a **database** solves: fast search by the field you need, storage on disk between runs, and concurrent access without confusion.

> A **database (DB)** is an organized collection of data managed by a dedicated program — a DBMS (database management system).

A simple analogy: a database is a smart warehouse. Every "shelf" has its own address, and the "warehouse robot" (the DBMS) quickly finds and hands over whatever is needed.

## The main types of databases

There are many kinds of databases, but in practice three keep coming up:

| Database type     | Description                              |
| ----------------- | ---------------------------------------- |
| Relational        | Data in linked tables — rows and columns |
| Key-value         | Fast access to a value by its key        |
| Document-oriented | Flexible documents in JSON format        |

## The most common DBMSs

Relational databases are what you'll encounter most: **PostgreSQL** and **MySQL** on servers, **SQLite** — inside phones, browsers and Python itself. Of the rest, **Redis** (key-value: caches and sessions) and **MongoDB** (documents) come up regularly.

The next three chapters are about relational databases, so from here on we talk only about them.

## Understanding check

**Why do applications store their users in a database rather than a plain list in memory?**

1. **Correct answer:** A database searches quickly by the field you need, keeps data between runs, and allows concurrent access — Fast search by the right field instead of scanning the whole list, data that survives a restart, and several clients writing at once without confusion — those are exactly the jobs a database exists for.

2. A database is always faster than any Python code — Speed depends on the task: accessing a single element by index is even faster with an in-memory list. The database wins at searching large volumes, reliability and concurrent access.

3. A Python list cannot hold more than 1000 elements — List size is limited only by RAM, not by the number 1000. The problem is not an element limit but search speed and losing data on restart.

4. With a database you don't need to write code — You still write queries — in SQL or through a library. A database saves you not from code but from manual searching, file-based storage and fighting over concurrent access.

In the next article we start practicing with **SQLite** — a database that's ideal for learning and already built into Python: we'll create our first DB and work with real data.
