---
meta:
    title: "Why Tests Matter and What Kinds Exist"
    description: "Why bugs are cheaper to catch early, the types of tests (unit, integration, E2E), the testing pyramid and the FIRST principles."
---

# Why tests matter and what kinds exist

You fixed one function, shipped the update — and a day later a user writes that something else broke, something that worked yesterday. A change in one place quietly affected another, and you were the last to find out.

Testing is how you learn about breakages like this right away, not from users. It's a set of checks that confirm your code does what it's expected to — and keeps doing it after every change.

## Why test at all

In 1999, NASA lost a $125 million Mars orbiter because of a simple mistake: the calculations mixed different unit systems. A single test could have caught the mismatch before launch.

That's an extreme case of a general rule — the later a bug is found, the more it costs to fix:

| When the bug is found   | Relative cost of fixing |
| ----------------------- | ----------------------- |
| During development      | 1x                      |
| During code review      | 2x                      |
| During QA testing       | 5x-10x                  |
| After release (in prod) | 30x-100x+               |

Beyond catching bugs early, tests give you a few more things:

- **confidence when changing code** — with a test suite you can refactor and add features boldly: if something breaks, the tests are the first to tell you;
- **living documentation** — tests show how the code is meant to be used and, unlike ordinary docs, never go stale;
- **better design** — if a function is hard to test, it's usually just built too complex;
- **protection from regressions** — that's the name for breakage of previously working code after new changes; the example below catches exactly those.

## Types of tests

Tests differ in the scale of what they check — from a single function to the whole system.

### Unit tests

They check the smallest, isolated parts of the program — individual functions, methods or classes. The goal is to make sure every "brick" of the code works correctly on its own.

```python
def add(a, b):
    return a + b

print(add(2, 3) == 5)
<output>
True
</output>
```

The check returned `True` — the function works as expected. Now imagine someone edited `add` and accidentally broke the logic:

```python
def add(a, b):
    return a + b + 1        # a bug that slipped in

print(add(2, 3) == 5)
<output>
False
</output>
```

The same check instantly returned `False`. That's what a unit test is: a small check that tells you by itself whether the function is intact — that's how regressions get caught. In the next article we'll see how to write such checks with `assert` and run them in batches.

### Integration tests

They check how several modules work together: for example, that after a user is created in the database, the login system recognizes them. Modules can be fine on their own and still fail to fit together — that's what gets caught here.

### End-to-end tests (E2E)

They check the whole system from the user's point of view, passing through every layer of the application. An example is the full signup scenario: fill in the form, submit the data, receive the email, log in.

## The testing pyramid

How many of each kind should you write? The usual answer is drawn as a pyramid:

| Level            | Characteristics |
| ---------------- | --------------- |
| Unit             | fast · many     |
| Integration      | in between      |
| End-to-end (E2E) | slow · few      |

The higher the level, the slower and more expensive the tests — so there are fewer of them. The bulk is fast unit tests: they point exactly at the place of the breakage.

## Principles of a good test (FIRST)

Five traits of a useful test spell the acronym FIRST:

- **F**ast: slow tests get run rarely;
- **I**ndependent: doesn't rely on other tests or the run order;
- **R**epeatable: gives the same result on every run;
- **S**elf-validating: tells you by itself whether it passed, with no manual comparison of results;
- **T**imely: written together with the code, not "someday later".

## Testing frameworks

Writing checks by hand, as in the `add` example, gets old fast: you want batch runs, reports, convenient comparisons. That's what frameworks are for, and Python has two main ones:

- **pytest** — third-party, with a concise syntax; that's where we'll start;
- **unittest** — built into the standard library.

## Understanding check

**Which statement about software testing is the most accurate?**

1. **Correct answer:** Testing helps discover bugs at early stages of development, reducing the cost of fixing them. — The earlier a bug is caught, the cheaper the fix — up to a hundredfold difference between development and production.

2. The main goal of testing is to reach 100% code coverage. — Coverage is a useful metric (we'll talk about it later), but 100% coverage by itself doesn't guarantee the absence of bugs. Test quality matters more.

3. Functional (E2E) tests are the fastest and should form the base of the testing pyramid. — According to the testing pyramid, the base is unit tests: they are the fastest and most numerous. End-to-end tests sit at the top of the pyramid.

4. The FIRST principle means tests should above all be Thorough, even if they are slow. — FIRST specifically stresses being Fast: slow tests get run rarely, and that makes them less useful.

In the next article — hands-on practice with `pytest`: your first real tests, the `assert` statement and batch runs.
