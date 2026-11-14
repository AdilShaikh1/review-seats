# Task 4 brief: the --output flag (branch feature/output-flag)

Read this first. It is your requirements.

## Where this fits
`tidy-csv` is a small Python CLI that cleans CSV files. Main holds the cleaner and its tests. This task adds a
`--output` flag so a user can write the cleaned CSV to a path instead of printing it to stdout.

## What to build
- `tidy_csv/cli.py` gains an `--output PATH` option. When it's given, the cleaned rows go to that path instead
  of stdout.
- `README.md` gains one line documenting the flag.

## Rules
- Don't run git and don't dispatch subagents.
- Keep the change small. Aim for about 40 changed lines.

## Report
Write the full report to `.superpowers/sdd/task-4-report.md`. It covers the files you changed, their line
counts, and any ruling you made.
