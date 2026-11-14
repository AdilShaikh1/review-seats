# Task 4 report: the --output flag (feature/output-flag)

Status: DONE. All changes are uncommitted. No git commands ran and no subagents were dispatched.

## Files and line counts

| File | Lines | Change |
|---|---|---|
| `tidy_csv/cli.py` | 58 (was 43) | `--output` option added |
| `README.md` | 19 (was 18) | one line on the new flag |

About 17 changed lines in `cli.py` and 1 in `README.md`.

### What changed
- `cli.py` adds an `--output PATH` option. When given, the new `write_rows()` helper writes the cleaned rows to
  that path with `open(path, "w", newline="")`, instead of printing them to stdout.
- `README.md` gains one line: "Pass `--output PATH` to write the cleaned rows to a file instead of stdout."

## Ruling
The new `write_rows()` helper opens its output with `newline=""`, so the file keeps exactly the newlines the
function writes. The brief didn't say which newline handling to use, so this stays an implementation choice
rather than a question for the user.

## Concerns
None.
