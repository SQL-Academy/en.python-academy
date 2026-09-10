---
meta:
    title: "Encapsulation"
    description: "How a class protects its data: the single-underscore convention, @property, and read-only attributes."
---

# Encapsulation in Python

Let's build a piggy-bank class: `deposit` adds money and refuses a negative amount. The check is there — but getting around it is trivial:

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance

    def deposit(self, amount):
        if amount > 0:
            self.balance += amount

account = BankAccount(1000)

account.deposit(-500)          # the check won't let this through
print(account.balance)
<output>
1000
</output>

account.balance = -1_000_000   # but this — go right ahead
print(account.balance)
<output>
-1000000
</output>
```

The check in `deposit` hasn't gone anywhere — we simply bypassed it: `balance` is a regular attribute, and you can assign anything to it. The class cannot enforce its own rules — for example, "the balance is never negative".

Encapsulation is the answer to this problem: an object has a **public interface** (how the outside world talks to it) and **internal state** (which the outside world shouldn't poke). External code calls methods; the class itself makes sure its data stays correct.

## The single-underscore convention

How do you tell internal from public? Python has a convention for that: an attribute or method whose name starts with an underscore is considered "internal" — don't touch it from outside:

```python
class BankAccount:
    def __init__(self, balance):
        self._balance = balance   # underscore = "internal"

    def get_balance(self):
        return self._balance

    def deposit(self, amount):
        if amount > 0:
            self._balance += amount

    def withdraw(self, amount):
        if 0 < amount <= self._balance:
            self._balance -= amount

account = BankAccount(1000)
account.deposit(500)
account.withdraw(2000)        # more than the balance, ignored
print(account.get_balance())
<output>
1500
</output>
```

From the outside only `deposit`, `withdraw`, and `get_balance` are available: everything you need to work with the account. You **technically can** still poke `_balance` from outside (Python doesn't forbid it), but the convention says: don't, otherwise you're bypassing the class's checks.

The Python community states this philosophy as **"we're all consenting adults here"**. The language doesn't forbid — it signals that you shouldn't touch this. Responsibility is on the programmer.

outside code → through methods → `deposit()`, `withdraw()`, `get_balance()`; directly → `_balance`. The account is used through its methods, while `_balance` is changed only by the class itself.

## Properties: an attribute on the outside, a method on the inside

Often you want `account.balance` to **look** like a regular attribute from the outside, while underneath there's actually a method with validation. That's what `@property` is for.

The line with `@` above a method is a **decorator**: a device that changes how the method behaves. Decorators get their own lesson later in the course; for now it's enough to know what these two do:

```python
class Account:
    def __init__(self, balance):
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):
        if value < 0:
            raise ValueError("Balance cannot be negative")
        self._balance = value

account = Account(1000)

# Used like a regular attribute:
print(account.balance)
<output>
1000
</output>

account.balance = 500   # triggers the setter with validation
print(account.balance)
<output>
500
</output>

# Trying to set a negative value:
try:
    account.balance = -100
except ValueError as e:
    print(f"Error: {e}")
<output>
Error: Balance cannot be negative
</output>
```

`@property` turns a method into a "computed attribute": from the outside, `account.balance` is read without parentheses. `@balance.setter` defines what happens on assignment: from the outside it looks like a plain assignment, but inside the class the validation kicks in.

The example uses `raise` and `try/except` — that's the error mechanism: `raise` aborts the operation and signals that a value is invalid, while `try/except` catches it in the calling code. They have their own lesson later in the course; here it's enough to see that the check in the setter prevents an invalid value from being stored.

## Read-only properties

If a `@property` has no setter, the attribute becomes read-only. This is handy for **computed** values that don't make sense to "set" from outside:

```python
import math

class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return math.pi * self.radius ** 2

circle = Circle(5)
print(f"Radius: {circle.radius}, area: {round(circle.area, 2)}")
<output>
Radius: 5, area: 78.54
</output>

# You can't assign to area (there's no setter)
try:
    circle.area = 100
except AttributeError as e:
    print(f"Error: {e}")
<output>
Error: property 'area' of 'Circle' object has no setter
</output>
```

`area` always returns the current value, and you can't assign to it — rightly so: the area isn't stored separately, it follows from the radius.

## What encapsulation gives you

The main point: the class becomes **responsible for its own data**. From outside there's no way (by convention) to bypass its checks and leave the object with incorrect data. If later you need to change how the data is stored (say, `_balance` becomes a dict with a transaction history), the outside code doesn't break, because it still talks to the class through the same public interface — `deposit`, `withdraw`, `balance`.

## Understanding check

**How do you set up an attribute that can be read from outside but not changed?**

1. **Correct answer:** @property without a setter — From the outside such an attribute reads like a regular one, and Python raises AttributeError when you try to assign to it.

2. A regular attribute without an underscore — A regular attribute can be changed from anywhere in the program — there is no write protection.

3. An attribute with a single underscore — A single underscore is only an "internal, do not touch" signal: technically it can still be changed from outside.

4. A get_x() method and an \_x attribute — Reading would require a method call with parentheses, and \_x itself stays changeable. In Python, @property is used instead.

In the next lesson we'll take on the third principle of OOP — polymorphism: how a single interface can work with objects of different classes, and why that makes code simpler.
