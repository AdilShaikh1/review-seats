---
tags: [review-seats]
max_turns: 30
timeout_seconds: 900
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

I'm the controller for the tidy-csv project. Branch feature/ci-checks holds two consecutive tasks. Task 5 is the
current task, finished and uncommitted. Task 6 is planned next, and its package is already in place. Decide,
for each task, which seats run and on which model tier, and record that decision in the ledger. Don't dispatch
any seats.

Input files: .superpowers/sdd/task-5-package-r1.md, .superpowers/sdd/task-6-package-r1.md,
.superpowers/sdd/progress.md
