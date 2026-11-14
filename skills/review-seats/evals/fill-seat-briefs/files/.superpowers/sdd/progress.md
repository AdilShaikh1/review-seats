# SDD ledger — tidy-csv

Repo: tidy-csv (local checkout)

- Task 1 to task 3 (feature/cleaner, feature/dedupe-rows, feature/cli-flags): merged. No open concerns carried
  forward.
- Task 4 (feature/output-flag, first of two tasks on its branch): implementer DONE; brief `task-4-brief.md`,
  report `task-4-report.md`.
  - ruling: the new `write_rows()` helper opens its output with `newline=""`, so the file keeps exactly the
    newlines it writes (implementer's ruling, accepted).
  - gate: 0 failed checks; ship list `task-4-ship-list.md`; `cli.py` needs arguments, so the audit hook didn't
    run it and the gate marked it side effects not checked; no audit log.
  - next: fill the round-1 seat briefs and decide which seats run.
