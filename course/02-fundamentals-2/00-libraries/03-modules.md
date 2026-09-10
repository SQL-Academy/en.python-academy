---
meta:
    title: "Modules in Python"
    description: "How to create, import, and use your own modules in Python: code organization, namespaces, and best practices."
---

# Modules in Python

So far every program of yours has fitted into a single file, and that was enough. But one day you write a function that would come in handy in the next task too, and the only way to move it there is to copy it. Copy it into three scripts, find a bug, and you are fixing it in three places.

So we need a way to keep code in a separate file and pull only what we need out of it. A file like that is called a module.

## You already use modules

The line `import math` has come up in the chapters on libraries. Let's look closer at what it actually does.

```python
import math

print(math.sqrt(144))
<output>
12.0
</output>
print(type(math))
<output>
<class 'module'>
</output>
```

`import` doesn't paste somebody else's file into yours. It creates a **module object** and puts it in a variable called `math`. From then on `math.sqrt` is an ordinary attribute access, exactly like `"text".upper()`.

> A module is a file with a `.py` extension (a few built-in ones such as `math` have no file at all, they are baked into the interpreter). Everything defined in it (functions, classes, variables) becomes an attribute of the module object after the import.

## Creating your own module

Now let's make such a file ourselves. Say we have a small script that works out prices: it adds VAT and applies discounts. The calculation itself goes into `prices.py`, while `main.py` keeps only the printing of results:

**prices.py**

```python
"""Price calculations: VAT and discounts."""

VAT = 0.2


def with_vat(price):
    """Price including VAT."""
    return round(price * (1 + VAT), 2)


def discount(price, percent):
    """Price after a percentage discount."""
    return round(price * (1 - percent / 100), 2)

```

**main.py**

```python
import prices

print(prices.with_vat(100))  # 120.0
print(prices.discount(1000, 15))  # 850.0
print(prices.VAT)  # 0.2

```

`main.py` says `import prices`, and Python finds `prices.py` in the same folder. The module name comes from the file name: drop the `.py` and you get `prices`. That is all the import needs.

Since the module name is the file name, name your files in snake_case: `user_interface.py`, not `UserInterface.py`. And not `random.py`: a file named after a standard library module shadows the real one.

## A module's code runs exactly once

On an import Python executes the file top to bottom. In `boot.py` below the `print` sits at the top level, not inside a function, so it fires on import. Let's import that module on two consecutive lines:

**boot.py**

```python
print("boot.py is running")

READY = True

```

**main.py**

```python
import boot
import boot

print("and there were two import lines")

```

```text
boot.py is running
and there were two import lines
```

Even though there were two `import` lines, the file ran once: Python remembers the modules it has already finished and on a repeated import hands back the same object. Hence the practical takeaway: the top level of a module holds definitions, not actions — whatever is written there runs on the very first import, even if none of its functions are ever used.

## from ... import: the name moves in with you

Typing `prices.` before every call gets tiring. The `from ... import` form takes a specific name out of the module and puts it straight into your program. In the example below both import forms sit side by side on purpose, so that the last line can compare the result — you wouldn't write it that way in ordinary code.

**prices.py**

```python
"""Price calculations: VAT and discounts."""

VAT = 0.2


def with_vat(price):
    """Price including VAT."""
    return round(price * (1 + VAT), 2)


def discount(price, percent):
    """Price after a percentage discount."""
    return round(price * (1 - percent / 100), 2)

```

**main.py**

```python
import prices
from prices import with_vat

print(with_vat(100))  # 120.0
print(with_vat is prices.with_vat)  # True

```

The last line is the important one. No copy of the function was made: `with_vat` and `prices.with_vat` are two names for one object. `from ... import` isn't "another way to import", it's an extra step: first the module is imported in full (with all its code), then one of its names is copied into your namespace.

So you have three forms, and you choose between them like this:

- **You need many names from the module, or it matters where a name came from** — keep the prefix:

    ```python
    import prices

    print(prices.with_vat(100))
    ```

- **You need two or three functions and call them often** — take them by name, the code gets shorter:

    ```python
    from prices import with_vat, discount

    print(with_vat(100))
    ```

- **The module name is long or already taken by a variable of yours** — bring it in under a short name:

    ```python
    import prices as p

    print(p.with_vat(100))
    ```

That last form is exactly why people write `import pandas as pd`.

## Telling a run apart from an import

As we saw, an import executes the whole code of a module. So the code inside `prices.py` runs in both cases:

```bash
python prices.py   # launching the module itself
python main.py     # launching the program that imports it
```

But sometimes code is only wanted when the file itself is launched. A check along the lines of "do the functions compute the right thing", say: handy for the author, pure noise for whoever imports the module.

The `__name__` variable, which every module has, is what tells the two apart. On an import it holds the module's name, here `"prices"`. In a file that was launched directly it always holds `"__main__"`. So a condition like this is true only on a direct launch:

```python
if __name__ == "__main__":
    print("check:", with_vat(100))
```

In `prices.py` below that check sits at the very bottom, while `main.py` on the next tab simply imports the module:

**prices.py**

```python
"""Price calculations: VAT and discounts."""

VAT = 0.2


def with_vat(price):
    """Price including VAT."""
    return round(price * (1 + VAT), 2)


def discount(price, percent):
    """Price after a percentage discount."""
    return round(price * (1 - percent / 100), 2)


if __name__ == "__main__":
    print("check:", with_vat(100))

```

**main.py**

```python
import prices

print("total:", prices.with_vat(100))

```

```text
total: 120.0
```

We launched `main.py`, and the output holds only its own line: `__name__` inside `prices.py` equals `"prices"`, the condition is false, the check stayed quiet. Launching the module directly would have printed `check: 120.0`.

This is how one file plays two roles: a set of functions for other code, and a standalone program. That block is where you put everything that must not happen on import: starting the program, parsing command-line arguments, checks like ours.
