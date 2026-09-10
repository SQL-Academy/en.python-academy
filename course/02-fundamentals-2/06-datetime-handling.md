---
meta:
    title: "Working with Date and Time in Python"
    description: "The datetime module: three classes, arithmetic with timedelta, strftime and strptime, ISO 8601 and zoneinfo time zones."
---

# Working with Date and Time in Python

Suppose we have a log file with lines like `2026-05-20 14:30:42 ERROR ...`, and we need to count how many errors happened in the last 24 hours. That means: parse the date string into an object, compute the difference from "now", and (if showing the user) return it in their time zone and a friendly format.

These three operations — parsing a string, arithmetic, formatting — are the bread and butter of dates in Python. They all live in the standard `datetime` module.

## Three classes: datetime, date, time

The `datetime` module has three main classes, and it matters which one you pick:

- **`datetime`**: a specific moment in time, down to microseconds. The right choice 90% of the time.
- **`date`**: just the date, no time. Birthdays, deadlines, order dates.
- **`time`**: just the time of day, no date. A meeting slot in a calendar.

```python
from datetime import datetime, date, time

# A moment in time
now = datetime.now()
print(now)
<output>
2026-05-20 14:30:25.123456
</output>

# Just a date
birthday = date(1990, 5, 15)
print(birthday)
<output>
1990-05-15
</output>

# Just a time of day
meeting = time(14, 30)
print(meeting)
<output>
14:30:00
</output>
```

Each class has attributes for its own components:

- `date` has `.year`, `.month`, `.day`;
- `time` has `.hour`, `.minute`, `.second`, `.microsecond`;
- `datetime`, which combines date and time, has all of them.

```python
from datetime import datetime

now = datetime(2026, 5, 20, 14, 30)
print(now.year, now.month, now.day)
<output>
2026 5 20
</output>
print(now.weekday())   # 0 = Monday, 6 = Sunday
<output>
2
</output>
```

## Date arithmetic: timedelta

You can add and subtract dates directly: the result is a `timedelta` (a duration), or a new `datetime`.

```python
from datetime import datetime, timedelta

now = datetime(2026, 5, 20, 14, 30)

# Add an interval
week_later = now + timedelta(days=7)
print(week_later)
<output>
2026-05-27 14:30:00
</output>

# Difference between moments is a timedelta
deadline = datetime(2026, 6, 1)
delta = deadline - now
print(delta)
<output>
11 days, 9:30:00
</output>
print(delta.days, delta.total_seconds())
<output>
11 984600.0
</output>
```

`timedelta` accepts `days`, `hours`, `minutes`, `seconds`, `weeks` — but **not** `months` or `years`, because their length varies (28 or 29 days in February, 365 or 366 in a year). For "add N months" the third-party library `dateutil` covers the gap.

## strftime and strptime: between object and string

In real code, dates constantly flow through strings — APIs, logs, databases. Mnemonic to remember which method does what:

- **`strftime`** — **f**ormat: object → string
- **`strptime`** — **p**arse: string → object

`"2026-05-20 14:30"` → `strptime` → datetime object → `strftime` → `"20.05.2026 14:30"`.

```python
from datetime import datetime

# Object → string
now = datetime(2026, 5, 20, 14, 30)
print(now.strftime("%d.%m.%Y %H:%M"))
<output>
20.05.2026 14:30
</output>
print(now.strftime("%A, %d %B %Y"))
<output>
Wednesday, 20 May 2026
</output>

# String → object
parsed = datetime.strptime("20.05.2026 14:30", "%d.%m.%Y %H:%M")
print(parsed)
<output>
2026-05-20 14:30:00
</output>
```

The format is described by a string with directives like `%Y`, `%m`, `%d`:

| Directive | Description          | Example   |
| --------- | -------------------- | --------- |
| `%Y`      | Year, 4 digits       | 2026      |
| `%m`      | Month (01-12)        | 05        |
| `%B`      | Full month name      | May       |
| `%d`      | Day of month (01-31) | 20        |
| `%A`      | Full weekday name    | Wednesday |
| `%H`      | Hour (00-23)         | 14        |
| `%M`      | Minutes (00-59)      | 30        |
| `%S`      | Seconds (00-59)      | 42        |

Now let's assemble the example from the introduction: take a log line, turn the date into an object, and check whether the error happened within the last 24 hours:

```python
from datetime import datetime, timedelta

line = "2026-05-20 14:30:42 ERROR disk full"

logged_at = datetime.strptime(line[:19], "%Y-%m-%d %H:%M:%S")
now = datetime(2026, 5, 21, 10, 0)

print(now - logged_at)
<output>
19:29:18
</output>
print(now - logged_at < timedelta(days=1))
<output>
True
</output>
```

The slice `line[:19]` cuts off the first 19 characters — exactly the date and time, without the message tail.

## ISO 8601: the date interchange standard

When a date crosses a system boundary (API, JSON, database), use **ISO 8601**: `2026-05-20T14:30:00`. `datetime` has built-in methods for this format, and they're faster and more robust than `strftime`/`strptime`:

```python
from datetime import datetime

now = datetime(2026, 5, 20, 14, 30)

# To ISO string
iso_string = now.isoformat()
print(iso_string)
<output>
2026-05-20T14:30:00
</output>

# And back
parsed = datetime.fromisoformat("2026-05-20T14:30:00")
print(parsed)
<output>
2026-05-20 14:30:00
</output>
```

Rule of thumb: inside your program keep dates as `datetime` objects; when crossing the boundary (out to JSON or a DB) use `.isoformat()`; when reading from outside, use `fromisoformat()`. A custom `strftime` format is only needed when you're showing dates to a human.

## Time zones: naive vs aware

`datetime.now()` with no arguments returns a "naive" datetime — it has no time zone information. Common trap: the program works fine on your laptop, then runs on a server in a different country and silently shows times 7 hours off.

The right thing is to work with **aware** datetimes — ones that carry a time zone. Since Python 3.9 there's a built-in `zoneinfo` module that knows real-world time zones, including daylight saving:

```python
from datetime import datetime
from zoneinfo import ZoneInfo

# Aware datetimes in UTC and Moscow
utc_now = datetime(2026, 5, 20, 14, 30, tzinfo=ZoneInfo("UTC"))
moscow_now = utc_now.astimezone(ZoneInfo("Europe/Moscow"))

print(utc_now)
<output>
2026-05-20 14:30:00+00:00
</output>
print(moscow_now)
<output>
2026-05-20 17:30:00+03:00
</output>
```

Zone names are standardized (IANA tz database): `"Europe/Moscow"`, `"America/New_York"`, `"Asia/Tokyo"`. The typical practice when storing dates in a database: always store in UTC, convert to the user's local zone only when displaying.

> Older code used the third-party `pytz` library for time zones. Since Python 3.9, `zoneinfo` is built in and covers the same use cases — `pytz` is no longer needed.

## The time module

Beyond `datetime`, there's a lower-level `time` module. Two of its functions come up often:

```python
import time

# Current moment as a Unix timestamp (seconds since January 1, 1970)
print(time.time())
<output>
1779373825.123456
</output>

# Pause for N seconds
print("Start")
time.sleep(0.1)
print("0.1 seconds passed")
<output>
Start
0.1 seconds passed
</output>
```

`time.time()` returns a Unix timestamp — a single number, time-zone-independent, common in databases and logs. The `fromtimestamp` method converts it back to a `datetime` — you pass it the number and a time zone.

## Understanding check

**Which class from the datetime module represents a duration of time?**

1. datetime — The datetime class represents a specific moment, not a duration.

2. date — The date class represents only a date, without time.

3. time — The time class represents only a time of day, without a date.

4. **Correct answer:** timedelta — timedelta represents a duration and is used for date arithmetic: the difference between two datetimes is a timedelta, and you can add a timedelta to a datetime to get a new moment.

`datetime` will keep coming up: working with APIs (dates arrive as ISO 8601 strings), databases (storing event times), logging. The main rule: inside your program keep dates as objects; convert to strings only at the boundary with the outside world.

In the next lesson — the final chapter of the module: type annotations, hints about what values a function takes and returns.
