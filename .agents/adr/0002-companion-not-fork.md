# Companion, not a fork

review-seats tunes `superpowers:subagent-driven-development` (SDD) for a user who keeps every git write, wants small PRs and pays for each review round. It never copies SDD's text into this repo and never edits SDD's files. Every rule here either overrides one of SDD's sections or adds to it, and the parts it doesn't touch stay SDD's as written.

SDD keeps evolving under its own maintainers, on its own release schedule. A companion rides on every later SDD release instead of freezing a copy of SDD's text at some point in time. A fix or a feature SDD ships reaches review-seats users too, without a second edit in this repo.

SKILL.md section 1 lists the overrides by section, so a reader can tell what review-seats changes and what it leaves to SDD.

The cost is a dependency on SDD's own behaviour and file names. A section SDD renames, removes or restructures can break review-seats' override of it, and nothing in this repo catches that on its own. The fix is to watch SDD's releases and update the override that drifted, not to copy SDD's files here against that risk.
