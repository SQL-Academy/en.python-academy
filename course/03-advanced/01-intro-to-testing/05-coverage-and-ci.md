---
meta:
    title: "Code coverage and CI: automating tests"
    description: "Measure test coverage with pytest-cov and run tests automatically with GitHub Actions on every push. The basic production setup."
---

# Code coverage and CI

Forty tests in the repo, all green. Except the last time anyone ran them was eleven days ago — and a dozen changes have landed on `main` since. They're green only on paper: nobody knows whether they still pass. For tests to actually guarantee anything, you need two things:

1. **Coverage**: know what parts of the code your tests actually exercise, and what they don't.
2. **CI** (Continuous Integration): so tests run automatically on every push, not "sometime" by hand.

## Coverage via pytest-cov

**Coverage** is the percentage of source code executed during a test run. High coverage doesn't guarantee absence of bugs (you can cover a line but not check it well), but **low coverage** clearly shows "this code isn't tested".

For pytest projects the standard tool is the `pytest-cov` plugin:

```bash
pip install pytest pytest-cov
```

Run with the `--cov` flag and a package name:

```bash
pytest --cov=app tests/
```

You get a summary:

```text
---------- coverage: platform ... -- Python ... -----------
Name              Stmts   Miss  Cover
-------------------------------------
app/users.py        25      5    80%
app/orders.py       18      0   100%
-------------------------------------
TOTAL               43      5    88%
```

- `Stmts`: how many statements are in the file.
- `Miss`: how many didn't execute during the tests.
- `Cover`: percentage of coverage.

For a detailed report (with line-by-line highlighting in a browser) use HTML:

```bash
pytest --cov=app --cov-report=html
```

That creates an `htmlcov/` folder; open `htmlcov/index.html` to see per-line coverage.

### About percentages

Chasing 100% usually isn't worth it. A realistic target is **80-90%** for business logic. What's left uncovered is more useful to look at by hand: "is this critical code or just a getter/setter?". Coverage is a **signal about gaps**, not a goal in itself.

## What CI is

**CI** is automatic test (or any other check) runs on repository changes. On GitHub it's **GitHub Actions**, on GitLab it's the built-in GitLab CI, there's also CircleCI, Jenkins, and others. The principle is the same everywhere: on every `git push`, a configured pipeline runs.

![Illustration: git push → GitHub Actions runs automatically → pytest + coverage → branches to passed (green) or failed (red)](https://python-academy.org/static/guidePage/coverage-and-ci/ci-pipeline-en.webp "CI runs tests on every push")

## A minimal CI on GitHub Actions

Put `.github/workflows/tests.yml` at the root of the repo:

```yaml
name: tests

on:
    push:
        branches: [main]
    pull_request:
        branches: [main]

jobs:
    test:
        runs-on: ubuntu-latest
        steps:
            - uses: actions/checkout@v4
            - uses: actions/setup-python@v5
              with:
                  python-version: "3.12"
            - run: pip install -r requirements.txt
            - run: pytest --cov=app
```

What this does:

1. Triggers on `push` and `pull_request` to `main`.
2. Clones the repo and installs Python 3.12.
3. Installs dependencies from `requirements.txt`.
4. Runs the tests with coverage.

If even one test fails, the pipeline goes red and the PR is blocked (if you turn on that requirement in the repo settings).

## What you get

- **Errors caught immediately**, not a week later in production.
- **Tests always run**: no relying on "I'll run them locally before merge".
- **Pull requests show** whether tests passed; reviewers see it right away.
- **Coverage is tracked**: if a PR adds code without tests, the report shows it.

## Understanding check

**What is true about coverage and CI?**

1. **Correct answer:** Coverage shows what percentage of source code is executed during tests — Correct. It's a signal about gaps in testing, but not a guarantee that the code itself is correct.

2. For pytest integration you use coverage.py directly — coverage.py is the underlying library, but for pytest integration you usually install the pytest-cov wrapper plugin.

3. CI is only for running tests on a schedule — CI runs tests automatically on every push/PR. Scheduled runs are a separate use case.

4. 100% coverage guarantees no bugs — No. You can cover a line but not check it well, or miss an edge case. High coverage is a useful signal, not a correctness guarantee.

That wraps up the testing module. You can write pytest tests, use fixtures and parametrization, mock dependencies, read `unittest` code in legacy projects, measure coverage, and set up CI. That's enough to jump into any production project; the rest you'll pick up on the job.
