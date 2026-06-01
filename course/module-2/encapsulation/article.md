# Encapsulation in Python

Suppose we're writing a bank account class:

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance

account = BankAccount(1000)
print(account.balance)        # 1000
account.balance = -1_000_000  # oops
```

The class doesn't object: `balance` is a public attribute, and you can assign anything to it — including things that should never be possible. The class fails to enforce its **invariants** (rules that must always hold, for example "the balance is non-negative").

Encapsulation is the idea that an object has a **public interface** (how the outside world talks to it) and **internal state** (which the outside world shouldn't poke). External code calls methods; the class itself watches over its data and keeps it valid.

## The single-underscore convention

Python has no `private` keyword. Instead there's a convention: an attribute or method whose name starts with an underscore is considered "internal", meaning don't touch from outside:

```python-executable
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
# Output: 1500
```

From the outside only `deposit`, `withdraw`, and `get_balance` are available: everything you need to work with the account. You **technically can** still poke `_balance` from outside (Python doesn't forbid it), but the convention says: don't, otherwise you're bypassing the class's checks.

The Python community states this philosophy as **"we're all consenting adults here"**. The language doesn't forbid — it signals that you shouldn't touch this. Responsibility is on the programmer.

## Properties: an attribute on the outside, a method on the inside

Often you want `account.balance` to **look** like a regular attribute from the outside, while underneath there's actually a method (for example, with validation). In Java you'd write `getBalance()` and `setBalance()`. In Python there's `@property`:

```python-executable
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
# Output: 1000

account.balance = 500   # triggers the setter with validation
print(account.balance)
# Output: 500

# Trying to set a negative value:
try:
    account.balance = -100
except ValueError as e:
    print(f"Error: {e}")
# Output: Error: Balance cannot be negative
```

The `@property` decorator turns a method into a "computed attribute". `@balance.setter` defines what happens on assignment. From the outside it all looks like `account.balance = 500`, but inside the class the validation kicks in.

> In Java and C++ people often write `get_x()` and `set_x()` methods. In Python that's not idiomatic: use `@property`.

## Read-only properties

If a `@property` has no setter, the attribute becomes read-only. This is handy for **computed** values that don't make sense to "set" from outside:

```python-executable
import math

class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value <= 0:
            raise ValueError("Radius must be positive")
        self._radius = value

    @property
    def area(self):
        return math.pi * self._radius ** 2

circle = Circle(5)
print(f"Radius: {circle.radius}, area: {circle.area:.2f}")
# Output: Radius: 5, area: 78.54

# Change the radius, the area is recomputed automatically
circle.radius = 7
print(f"Radius: {circle.radius}, area: {circle.area:.2f}")
# Output: Radius: 7, area: 153.94

# You can't assign to area (there's no setter)
try:
    circle.area = 100
except AttributeError as e:
    print(f"Error: {e}")
# Output: Error: property 'area' of 'Circle' object has no setter
```

`area` always returns the current value, and you can't assign to it: there shouldn't be a "set the area" operation, since it follows from the radius.

## What about double underscores?

You may sometimes see attributes with two leading underscores: `self.__balance`. That triggers Python's **name mangling**: the attribute is renamed inside the object to `_ClassName__balance`. It's useful in rare cases (for example, to make sure an attribute doesn't collide with a same-named attribute in a subclass) and is almost never seen in regular code. By default, use a single underscore.

## What encapsulation gives you

The main point: the class becomes **responsible for its own data**. From outside, there's no (by convention) way to bypass its checks and leave the object in an invalid state. If later you need to change how the data is stored (say, `_balance` becomes a dict with a transaction history), the outside code doesn't break, because it still talks to the class through the same interface — `deposit`, `withdraw`, `balance`.

## What's next?

In the next lesson we'll take on the third principle of OOP — polymorphism: how a single interface can work with objects of different classes, and why that makes code simpler.

---

**Which access level is most appropriate for an attribute that should be readable but should not be changed outside the class?**

