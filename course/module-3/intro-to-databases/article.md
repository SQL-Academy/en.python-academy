# Database Fundamentals in Python: What are Databases and Why We Need Them

Every day we use dozens of applications: checking social media,
shopping online, listening to music, booking tickets.
Behind each of them is a **database** — a system that instantly finds the right information among millions of records.

**Databases** are the fundamental technology of modern applications.
For Python developers, understanding database principles opens doors to creating scalable and reliable solutions.

## What is a Database?

> **Database (DB)** — an organized collection of data that is stored and managed by a specialized system (DBMS).

Simple analogy: a database is like a smart warehouse. Each "shelf" has its own address, and the "warehouse robot" (DBMS) can quickly find and deliver any needed information.

## Why Not Store Data in Python Lists?

Let's consider a simple example — storing users:

```python
# Storage in Python list
users = [
    {"id": 1, "name": "Anna", "email": "anna@example.com"},
    {"id": 2, "name": "Peter", "email": "peter@example.com"},
    # ... imagine a million users here
]

# Search user by email
def find_user_by_email(email):
    for user in users:  # Check the ENTIRE list! 😱
        if user["email"] == email:
            return user
    return None
```

**Problems with this approach:**

-   **Slow search** — need to check every user
-   **Memory limitations** — all data must fit in RAM
-   **Data loss** — if the program crashes, everything is lost
-   **Concurrency** — what if multiple users modify data simultaneously?

**Databases solve all these problems out of the box** — that's why they became the standard for serious applications.

## Main Types of Databases

There are many types of databases, but let's highlight the three most important:

### 1. Relational Databases

Organize data into **tables** with clear structure. The most popular type.

```sql
-- Example users table
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(150)
);
```

### 2. Key-Value Databases

Store simple "key-value" pairs. Very fast for simple operations.

```python
# Example in Redis — popular key-value DB
user_session = {
    "session:user123": "logged_in",
    "cart:user123": "[1,5,9]",  # Product IDs in cart
    "last_seen:user123": "2024-01-15 10:30"
}
```

### 3. Document-Oriented Databases

Store data as documents (JSON format). Flexible structure.

```python
# Example user document
{
    "name": "Anna",
    "email": "anna@example.com",
    "preferences": {
        "theme": "dark",
        "language": "en"
    }
}
```

## Popular Database Management Systems

Here's the top-8 most used DBMS in 2024:

1. **PostgreSQL** — powerful **relational** DB
2. **MySQL** — popular **relational** DB for web
3. **SQLite** 📱 — lightweight **relational** DB
4. **MongoDB** — leader in **document-oriented** DBs
5. **Redis** ⚡ — fast **key-value** DB
6. **Oracle Database** — enterprise **relational** DB
7. **Microsoft SQL Server** — **relational** DB from Microsoft
8. **Elasticsearch** — **document-oriented** search DB

**Conclusion:** relational databases (5 out of 8 positions) are the industry standard
due to their reliability and universality.

## What's Next?

In the next article, we'll start practicing with **SQLite** — the perfect database for learning, which is already built into Python.
You'll create your first DB and learn to work with real data.
