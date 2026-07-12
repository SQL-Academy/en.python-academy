# Working with Date and Time in Python

Suppose we have a log file with lines like `2026-05-20 14:30:42 ERROR ...`, and we need to count how many errors happened in the last 24 hours. That means: parse the date string into an object, compute the difference from "now", and (if showing the user) return it in their time zone and a friendly format.

These three operations — parsing a string, arithmetic, formatting — are the bread and butter of dates in Python. They all live in the standard `datetime` module.

## Three classes: datetime, date, time

The `datetime` module has three main classes, and it matters which one you pick:

-   **`datetime`**: a specific moment in time, down to microseconds. The right choice 90% of the time.
-   **`date`**: just the date, no time. Birthdays, deadlines, order dates.
-   **`time`**: just the time of day, no date. A meeting slot in a calendar.

```python-executable
from datetime import datetime, date, time

# A moment in time
now = datetime.now()
print(now)
# Output: 2026-05-20 14:30:25.123456

# Just a date
birthday = date(1990, 5, 15)
print(birthday)
# Output: 1990-05-15

# Just a time of day
meeting = time(14, 30)
print(meeting)
# Output: 14:30:00
```

All three have attributes for individual components: `.year`, `.month`, `.day`, `.hour`, `.minute`, `.second`, `.microsecond`.

```python-executable
from datetime import datetime

now = datetime(2026, 5, 20, 14, 30)
print(now.year, now.month, now.day)
# Output: 2026 5 20
print(now.weekday())   # 0 = Monday, 6 = Sunday
# Output: 2
```

## Date arithmetic: timedelta

You can add and subtract dates directly: the result is a `timedelta` (a duration), or a new `datetime`.

```python-executable
from datetime import datetime, timedelta

now = datetime(2026, 5, 20, 14, 30)

# Add an interval
week_later = now + timedelta(days=7)
print(week_later)
# Output: 2026-05-27 14:30:00

# Difference between moments is a timedelta
deadline = datetime(2026, 6, 1)
delta = deadline - now
print(delta)
# Output: 11 days, 9:30:00
print(delta.days, delta.total_seconds())
# Output: 11 985800.0
```

`timedelta` accepts `days`, `hours`, `minutes`, `seconds`, `weeks` — but **not** `months` or `years`, because their length varies (28 or 29 days in February, 365 or 366 in a year). For "add N months" the third-party library `dateutil` covers the gap.

## strftime and strptime: between object and string

In real code, dates constantly flow through strings — APIs, logs, databases. Mnemonic to remember which method does what:

-   **`strftime`** — **f**ormat: object → string
-   **`strptime`** — **p**arse: string → object

```python-executable
from datetime import datetime

# Object → string
now = datetime(2026, 5, 20, 14, 30)
print(now.strftime("%d.%m.%Y %H:%M"))
# Output: 20.05.2026 14:30
print(now.strftime("%A, %d %B %Y"))
# Output: Wednesday, 20 May 2026

# String → object
parsed = datetime.strptime("20.05.2026 14:30", "%d.%m.%Y %H:%M")
print(parsed)
# Output: 2026-05-20 14:30:00
```

The format is described by a string with directives like `%Y`, `%m`, `%d`:

| Directive | Description              | Example |
| --------- | ------------------------ | ------- |
| `%Y`      | Year, 4 digits           | 2026    |
| `%y`      | Year, 2 digits           | 26      |
| `%m`      | Month (01-12)            | 05      |
| `%B`      | Full month name          | January |
| `%b`      | Abbreviated month name   | Jan     |
| `%d`      | Day of month (01-31)     | 20      |
| `%A`      | Full weekday name        | Wednesday |
| `%a`      | Abbreviated weekday name | Wed     |
| `%H`      | Hour (00-23)             | 14      |
| `%I`      | Hour (01-12)             | 02      |
| `%M`      | Minutes (00-59)          | 30      |
| `%S`      | Seconds (00-59)          | 25      |

## ISO 8601: the date interchange standard

When a date crosses a system boundary (API, JSON, database), use **ISO 8601**: `2026-05-20T14:30:00`. `datetime` has built-in methods for this format, and they're faster and more robust than `strftime`/`strptime`:

```python-executable
from datetime import datetime

now = datetime(2026, 5, 20, 14, 30)

# To ISO string
iso_string = now.isoformat()
print(iso_string)
# Output: 2026-05-20T14:30:00

# And back
parsed = datetime.fromisoformat("2026-05-20T14:30:00")
print(parsed)
# Output: 2026-05-20 14:30:00
```

Rule of thumb: inside your program keep dates as `datetime` objects; when crossing the boundary (out to JSON or a DB) use `.isoformat()`; when reading from outside, use `fromisoformat()`. A custom `strftime` format is only needed when you're showing dates to a human.

## Time zones: naive vs aware

`datetime.now()` with no arguments returns a "naive" datetime — it has no time zone information. Common trap: the program works fine on your laptop, then runs on a server in a different country and silently shows times 7 hours off.

The right thing is to work with **aware** datetimes that carry a time zone. Since Python 3.9 there's a built-in `zoneinfo` module that knows real-world time zones, including daylight saving:

```python-executable
from datetime import datetime
from zoneinfo import ZoneInfo

# Aware datetimes in UTC and Moscow
utc_now = datetime(2026, 5, 20, 14, 30, tzinfo=ZoneInfo("UTC"))
moscow_now = utc_now.astimezone(ZoneInfo("Europe/Moscow"))

print(utc_now)
# Output: 2026-05-20 14:30:00+00:00
print(moscow_now)
# Output: 2026-05-20 17:30:00+03:00
```

Zone names are standardized (IANA tz database): `"Europe/Moscow"`, `"America/New_York"`, `"Asia/Tokyo"`. The typical practice when storing dates in a database: always store in UTC, convert to the user's local zone only when displaying.

> Older code used the third-party `pytz` library for time zones. Since Python 3.9, `zoneinfo` is built in and covers the same use cases — `pytz` is no longer needed.

## The time module

Beyond `datetime`, there's a lower-level `time` module. Two of its functions come up often:

```python-executable
import time

# Current moment as a Unix timestamp (seconds since January 1, 1970)
print(time.time())
# Output: 1779373825.123456

# Pause for N seconds
print("Start")
time.sleep(0.1)
print("0.1 seconds passed")
# Output:
# Start
# 0.1 seconds passed
```

`time.time()` returns a Unix timestamp — a single number, time-zone-independent, common in databases and logs. Convert it back to a `datetime` via `datetime.fromtimestamp(ts, tz=ZoneInfo("UTC"))`.

## What's next?

`datetime` will keep coming up: working with APIs (dates arrive as ISO 8601 strings), databases (storing event times), logging. The main rule: inside your program keep dates as objects; convert to strings only at the boundary with the outside world.

---

**Which class from the datetime module represents a duration of time?**

