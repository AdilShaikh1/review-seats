---
tags: [review-seats]
max_turns: 30
timeout_seconds: 900
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

I'm the controller for the pose-kit project. Branch feature/pose-fixes holds two consecutive tasks. Task 8 is
the current task, finished and uncommitted. Task 9 is planned next, and its package is already in place. Decide,
for each task, which seats run and on which model tier, and record that decision in the ledger. Don't dispatch
any seats.

Input files: CLAUDE.md, .superpowers/sdd/task-8-package-r1.md, .superpowers/sdd/task-9-package-r1.md,
.superpowers/sdd/progress.md
