# Task 6 review package, round 1 (BASE c4d5e6f, uncommitted)

## git diff --stat
 .github/workflows/test.yml | 28 ++++++++++++++++++++++++++++
 1 file changed, 28 insertions(+)

## new file: .github/workflows/test.yml
```yaml
name: test

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]
    steps:
      - uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -e .[test]
      - name: Run tests
        run: pytest -q
      - name: Run lint
        run: |
          pip install ruff
          ruff check tidy_csv
```

This is the project's first CI workflow. It runs on every push and pull request against main, and it installs
dependencies and runs the test suite and the linter on three Python versions.
