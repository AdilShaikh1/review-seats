# Overseer brief: <task name>

<!-- Note for the agent that fills in this brief. Copy this file into the workspace as
<task>-overseer-brief.md. Keep the one mode section that applies and delete the other two. Replace every
<placeholder> with a path or a name, then delete this note. Save a round-2 brief as
<task>-overseer-brief-r2.md, so it doesn't overwrite round 1. For a review with no plan, delete the task brief
and implementer's report lines. On the critical-areas line, write "none" when the project's CLAUDE.md lists no
critical areas or the diff touches none of them. -->

## Who reviews

Review as <persona>.
<!-- Use the domain persona the project's CLAUDE.md names. Without one, write "a senior engineer for <the
project's stack>". -->

Read `<contract path>` first. It holds the ground rules, the finding classes and the verdict format, and every
rule in it applies to this review.
<!-- The contract path is the absolute path of references/brief-contract.md in this skill's installed folder. -->

## Mode: per-task review, round 1

Read these files:
- the review package: `<package path>`
- the task brief: `<task brief path>`
- the implementer's report: `<implementer report path>`
- the decided items, which are the ledger's rulings and the user's decisions: `<decided items path>`

The critical areas this diff touches, from the project's CLAUDE.md: `<areas or none>`

Check each critical area named above first. Then check the logic and correctness of the change. Check that the
docs match the code. Treat each decided item as settled.

Write the report to `<report path>`.

## Mode: round 2

Read these files:
- the seat's own round-1 report: `<report path>`
- the diff since round 1: `<round-2 diff path>`
- the decided items, including any user decision made since round 1: `<decided items path>`
<!-- Build the diff since round 1 with git diff --no-index <round-1 package> <round-2 package>. -->

Give each round-1 blocking finding its verdict, as the contract's Round 2 section describes. Review only the text
the diff changes. Only the contract's finding classes block. Append the Round 2 section to `<report path>`.

## Mode: whole-branch review

This mode runs on a branch's last task, after that task's seats and before its command block.

Read these files:
- the branch package: `<branch package path>`
- the ledger, with every task's rulings, user decisions and documented limits: `<ledger path>`
<!-- The branch package holds git diff <merge-base> for tracked changes, where <merge-base> is the output of
git merge-base main HEAD. It adds the full text of each file that git ls-files --others --exclude-standard
lists. The uncommitted last task is therefore included. -->

The critical areas this diff touches, from the project's CLAUDE.md: `<areas or none>`

Check each critical area named above first. Then look for problems that cross tasks:
- contradictions between files
- interfaces that drifted from one task to the next
- docs that no longer match the code
- documented limits that now add up to a real defect

Write the report to `<report path>`.

This review gets one fix wave. The overseer then checks only that wave. It reads its own report and the wave's
diff at `<fix-wave diff path>`, applies the contract's Round 2 rules and appends a Round 2 section to the same
report. After that check, a finding blocks only in classes 1 to 3.
