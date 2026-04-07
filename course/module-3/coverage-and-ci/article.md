# Code Coverage and CI: Automating Python Tests

In the previous articles of this testing module, we studied the basics, learned to write tests using `pytest` and `unittest`, and mastered mocking techniques for isolating dependencies. Now it's time to look at two crucial aspects that take the testing process to the next level: **code coverage** and **continuous integration (CI)**.

These practices help not only to write tests but also to be confident in their quality and regular execution.

## What is Code Coverage?

> **Code coverage** is a metric that shows what percentage of your source code is executed during test runs.

Imagine you've written many tests. But how do you know if they actually check all the important parts of your application? Code coverage helps answer these questions:

-   Which lines, conditional branches, or functions of your code were touched by the tests?
-   Which parts remain "dark spots," untested by any test?

**It's important to understand:**

-   **High coverage (e.g., 90-100%) does not guarantee the absence of bugs.** Tests might cover the code but not check all logic or edge cases correctly.
-   **Low coverage definitely indicates insufficient testing.** If a significant portion of the code is not executed during tests, you cannot be confident in its correctness.

The goal of measuring coverage is not to blindly chase 100%, but to use this information to identify weaknesses in the test suite and consciously improve the testing strategy.

## Measuring Coverage with pytest-cov

For projects using `pytest`, the most popular tool for measuring coverage is the `pytest-cov` plugin. It integrates with `coverage.py` (the core library for measuring coverage in Python).

### Installation

```bash
pip install pytest pytest-cov
```

### Running Tests with a Coverage Report

To generate a coverage report, add the `--cov` flag to your usual `pytest` command. Specify the package or module for which you want to measure coverage.

```bash
pytest --cov=your_package_name tests/
```

(Replace `your_package_name` with the name of your package or the main source code directory, e.g., `src` or your application name).

`pytest-cov` will print a summary report to the terminal:

```text
---------- coverage: platform ... -- Python ... -----------
Name                      Stmts   Miss  Cover
---------------------------------------------
your_package_name/module1.py    25      5    80%
your_package_name/module2.py    18      0   100%
---------------------------------------------
TOTAL                         43      5    88%
```

-   `Stmts`: Number of executable statements (lines).
-   `Miss`: Number of statements not executed during tests.
-   `Cover`: Coverage percentage (`(Stmts - Miss) / Stmts`).

### Coverage Types

`coverage.py` (and thus `pytest-cov`) can measure different types of coverage:

-   **Statement coverage**: Was each line of code executed?
-   **Branch coverage**: Was every possible path in conditional statements (e.g., both `if`/`else` branches) taken? This is a stricter metric.
    To enable branch coverage analysis, you can use the `--cov-branch` flag.

### Creating HTML Reports for Detailed Analysis

HTML reports are very useful for a more visual and detailed analysis of coverage. They allow you to see directly in the browser which lines of code were covered and which were not.

```bash
pytest --cov=your_package_name --cov-report=html tests/
```

This command will create an `htmlcov` directory (by default). Open the `htmlcov/index.html` file in your browser to view the interactive report.

## Improving Testing Strategy with Coverage Reports

1.  **Set realistic goals**: Aim for high but reasonable coverage (e.g., 80-90%). Achieving 100% can be unjustifiably time-consuming for some parts of the code (e.g., simple code without logic or code handling very rare errors).
2.  **Analyze gaps, not just the percentage**: Look at _which_ parts of the code are not covered. If it's critical business logic, it needs to be covered by tests first.
3.  **Use coverage to write new tests**: If you see that an important conditional statement or function is not being tested, write tests that will exercise it.

## What is Continuous Integration (CI)?

> **Continuous Integration (CI)** is a software development practice where developers regularly (often several times a day) merge their code changes into a central repository. After each merge, builds and tests are run automatically.

If coverage helps assess the _quality_ of your tests, CI ensures their _regular and automatic execution_, guaranteeing that new changes do not break existing functionality.

**Key benefits of CI:**

-   **Early Bug Detection**: Integration problems and regressions are identified quickly while the changes are still fresh in the developer's mind.
-   **Automation of Routine**: Building and testing happen automatically, saving developers' time.
-   **Constant Feedback**: Developers quickly learn the test results for their changes.
-   **Improved Code Quality**: CI helps maintain a high level of quality, as passing tests become a mandatory requirement.
-   **Confidence in Releases**: If all tests pass in CI, it provides greater confidence when releasing new versions.

## Setting Up a Basic CI Workflow

There are many Continuous Integration (CI/CD) systems, such as GitLab CI, Jenkins, CircleCI, Travis CI, and others. Their operating principles are similar: automatically running tasks upon changes in the repository.

As an **example**, let's consider setting up using **GitHub Actions**, as it is a popular tool tightly integrated with GitHub.

To set up CI for a Python project with `pytest` and a coverage report in GitHub Actions:

1.  In the root of your repository, create a directory named `.github/workflows`.
2.  Inside this directory, create a YAML file, for example, `python-ci.yml`:

```yaml
name: Python CI Tests

on:
    push:
        branches: [main] # Simplified to one branch for the example
    pull_request:
        branches: [main]

jobs:
    test:
        runs-on: ubuntu-latest
        steps:
            - name: Checkout code
              uses: actions/checkout@v3

            - name: Set up Python
              uses: actions/setup-python@v4
              with:
                  python-version: '3.10' # Stick to one version for the example

            - name: Install dependencies
              run: |
                  python -m pip install --upgrade pip
                  pip install pytest pytest-cov
                  if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

            - name: Test with pytest and coverage
              run: |
                  pytest --cov=your_package_name --cov-report=term-missing
                  # Replace your_package_name with your package/directory name
```

**What this workflow does:**

1.  **Triggers**: Runs on `push` or `pull_request` events targeting the `main` branch.
2.  **Checkout**: Checks out your repository's code.
3.  **Set up Python**: Installs Python version 3.10.
4.  **Install dependencies**: Installs `pip`, `pytest`, `pytest-cov`, and dependencies from `requirements.txt`.
5.  **Test with pytest**: Runs tests using `pytest` and generates a coverage report in the terminal, showing missed lines (`term-missing`).

Integration with services like [Codecov.io](https://about.codecov.io/) or [Coveralls.io](https://coveralls.io/) allows uploading coverage reports (e.g., `coverage.xml`) and tracking coverage changes over time, displaying them directly in Pull Requests.

**Important Note**: While the configuration syntax differs across various CI/CD platforms, the core steps (Checkout, Set up Environment, Install dependencies, Run tests) remain conceptually the same.

## Best Practices for Coverage and CI

1.  **Keep CI Fast**: The faster the CI pipeline, the quicker developers get feedback.
2.  **Reliable CI Tests**: Avoid "flaky tests" (tests that sometimes pass and sometimes fail without code changes).
3.  **Integrate CI with Pull Requests**: Require CI tests to pass before merging branches.
4.  **Monitor Coverage**: Track code coverage and strive to improve it, especially for new code.
5.  **Fix Broken Builds Quickly**: Failed tests in CI should be addressed as a top priority.

## Conclusion

Measuring code coverage and setting up continuous integration are powerful tools for maintaining high software quality. They help development teams gain confidence in their code, detect problems quickly, and collaborate effectively.

Congratulations! You have completed this module on testing in Python. You now have the knowledge and tools to write quality tests for your projects.

---

**Which statement about code coverage and CI is most accurate?**
