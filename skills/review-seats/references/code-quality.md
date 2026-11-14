# Code-quality brief: <task name>

<!-- Note for the agent that fills in this brief. Copy this file into the workspace as
<task>-quality-brief.md. Keep the one mode section that applies and delete the other. Replace every
<placeholder> with a path or a name, then delete this note. Save a round-2 brief as
<task>-quality-brief-r2.md, so it doesn't overwrite round 1. For a review with no plan, delete the task brief
and implementer's report lines. -->

## How to review

Read `<contract path>` first. It holds the ground rules, the finding classes and the verdict format, and every
rule in it applies to this review.
<!-- The contract path is the absolute path of references/brief-contract.md in this skill's installed folder. -->

Call the Skill tool with "ponytail-review", then with "ponytail". Both skills are required, and this seat has no
fallback. When either one fails to load, stop and say so in the report.

Review the diff against both skills. The lens covers:
- duplication
- padding
- needless abstraction
- names
- comments that restate the code
- prose voice in docs

ponytail-review and ponytail set what to look for. The contract sets the report format and which findings block,
so a ponytail "Should fix" item blocks only when it falls in one of the contract's finding classes.

## Mode: round 1

Read these files:
- the review package: `<package path>`
- the task brief: `<task brief path>`
- the implementer's report: `<implementer report path>`
- the decided items, which are the ledger's rulings and the user's decisions: `<decided items path>`

Treat each decided item as settled. Write the report to `<report path>`.

## Mode: round 2

Read these files:
- the seat's own round-1 report: `<report path>`
- the diff since round 1: `<round-2 diff path>`
- the decided items, including any user decision made since round 1: `<decided items path>`
<!-- Build the diff since round 1 with git diff --no-index <round-1 package> <round-2 package>. -->

Give each round-1 blocking finding its verdict, as the contract's Round 2 section describes. Review only the text
the diff changes. Only the contract's finding classes block. Append the Round 2 section to `<report path>`.
