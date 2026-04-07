# Working with Date and Time in Python

Time is one of the most important concepts in programming. Like a skilled clockmaker who understands every movement of the mechanism, a programmer must be able to work with different time formats, account for time zones, and precisely plan events. Python provides powerful tools for working with date and time that will help you create reliable applications. ⏰

## What are date and time in programming?

> Date and time in programming are ways to represent and manipulate temporal data, including the current moment, specific dates, time intervals, and their transformations.

In Python, working with date and time is mainly done through the built-in `datetime` module, which provides classes and functions for:

1. **Representing moments in time** — specific dates and times
2. **Working with intervals** — time periods between events
3. **Formatting and parsing** — conversion between strings and time objects
4. **Arithmetic operations** — addition, subtraction of time periods

## The datetime module

The `datetime` module is the main tool for working with date and time in Python. It contains several important classes:

### The datetime class

The most used class for working with a specific moment in time:

```python
from datetime import datetime

# Getting current date and time
now = datetime.now()
print(f"Now: {now}")

# Creating a specific date
specific_date = datetime(2024, 12, 31, 23, 59, 59)
print(f"New Year: {specific_date}")

# Getting individual components
print(f"Year: {now.year}")
print(f"Month: {now.month}")
print(f"Day: {now.day}")
print(f"Hour: {now.hour}")
print(f"Minute: {now.minute}")
```

### The date class

For working with dates only (without time):

```python
from datetime import date

# Getting current date
today = date.today()
print(f"Today: {today}")

# Creating a specific date
birthday = date(1990, 5, 15)
print(f"Birthday: {birthday}")

# Getting day of week (0 = Monday, 6 = Sunday)
print(f"Day of week: {today.weekday()}")
```

### The time class

For working with time only (without date):

```python
from datetime import time

# Creating time
meeting_time = time(14, 30, 0)  # 14:30:00
print(f"Meeting time: {meeting_time}")


```

## Formatting date and time

### The strftime() method

Converting time objects to strings using formatting directives:

```python
from datetime import datetime

now = datetime.now()

# Various formats
print(now.strftime("%Y-%m-%d"))
print(now.strftime("%d.%m.%Y"))
print(now.strftime("%A, %d %B %Y"))
print(now.strftime("%d/%m/%Y %H:%M"))
```

Main formatting directives:

| Directive | Description              | Example |
| --------- | ------------------------ | ------- |
| `%Y`      | Year (4 digits)          | 2024    |
| `%y`      | Year (2 digits)          | 24      |
| `%m`      | Month (01-12)            | 01      |
| `%B`      | Full month name          | January |
| `%b`      | Abbreviated month name   | Jan     |
| `%d`      | Day of month (01-31)     | 15      |
| `%A`      | Full weekday name        | Monday  |
| `%a`      | Abbreviated weekday name | Mon     |
| `%H`      | Hour (00-23)             | 14      |
| `%I`      | Hour (01-12)             | 02      |
| `%M`      | Minute (00-59)           | 30      |
| `%S`      | Second (00-59)           | 25      |

### The strptime() method

Parsing strings into time objects:

```python
from datetime import datetime

# Parsing various formats
date1 = datetime.strptime("2024-01-15", "%Y-%m-%d")
print(f"Date 1: {date1}")

date2 = datetime.strptime("15/01/2024 14:30", "%d/%m/%Y %H:%M")
print(f"Date 2: {date2}")


```

## Arithmetic operations with dates

### The timedelta class

Represents a duration — the difference between two moments:

```python
from datetime import datetime, timedelta

now = datetime.now()
print(f"Now: {now}")

# Adding time
future = now + timedelta(days=7)
print(f"In a week: {future}")

# Difference between dates
birthday = datetime(2024, 5, 15)
time_until_birthday = birthday - now
print(f"Days until birthday: {time_until_birthday.days} days")
```

## Working with time zones

> A time zone is a region on Earth where the same standard time is used. The world is divided into 24 time zones, each differing from the next by one hour.

Time zones are important when working with users from different countries or when planning events.
Without considering them, problems with meeting times and data processing may occur.

### Basic time zone operations

```python
from datetime import datetime, timezone, timedelta

# UTC
utc_now = datetime.now(timezone.utc)
print(f"UTC: {utc_now}")

# Creating a custom time zone
moscow_tz = timezone(timedelta(hours=3))
moscow_time = utc_now.astimezone(moscow_tz)
print(f"Moscow: {moscow_time}")
```

## The time module

Additional time functionality:

```python
import time

# Current time in seconds since Unix epoch (January 1, 1970)
timestamp = time.time()
print(f"Timestamp: {timestamp}")

# Execution delay
print("Start")
time.sleep(1)  # Pause for 1 second
print("1 second passed")
```

## Understanding check

**Which class from the datetime module is used to represent a time interval?**


## Conclusion

Working with date and time is a fundamental skill in programming.
Python provides rich capabilities through the `datetime` and `time` modules, allowing elegant solutions from simple formatting to complex calculations with time zones.
