# Security brief: <task name>

<!-- Note for the agent that fills in this brief. Copy this file into the workspace as
<task>-security-brief.md. Keep the one mode section that applies and delete the other. Replace every
<placeholder> with a path or a name, then delete this note. Save a round-2 brief as
<task>-security-brief-r2.md, so it doesn't overwrite round 1. For a review with no plan, delete the task brief,
implementer's report and hand-off text lines. Keep the ship list line, because the gate builds one whenever this
seat runs. Delete the audit log line when no script changed. When a changed script needed arguments or wasn't
Python, so it didn't run, keep the audit log line and write "none" with that reason instead of deleting it. In
round 2, delete the hand-off text, ship list or audit log line when that file didn't change. When the project's
CLAUDE.md names no term list, write "none" in place of its path. -->

## How to review

Read `<contract path>` first. It holds the ground rules, the finding classes and the verdict format, and every
rule in it applies to this review.
<!-- The contract path is the absolute path of references/brief-contract.md in this skill's installed folder. -->

The local gate already listed what would ship and ran the audit hook when a script changed. This seat reads the
saved output and doesn't rerun anything. A leak or a security vulnerability in any area below falls in the
contract's class 1.

Review the change against these five areas:

1. **Leaks through output.** The change must not print secrets, tokens, private paths or private terms. That
   covers stdout and stderr, error messages and tracebacks, logs and generated files. Flag each place where such
   a value can reach output.
2. **Private data from the term list.** The project keeps a gitignored term list, and its CLAUDE.md names the
   path. Check every shipped file and the drafted hand-off text against it.
   - Report a hit by term index and `file:line` only. The term index is the term's line number in the list.
     Never quote the term, in the report or anywhere else.
   - With no term list, say so under "what the seat didn't check".
   - The public repo never holds private patterns. Flag any private pattern in a shipped file, even as an
     example.
3. **Side effects.** The gate ran each new or changed script that runs on the user's machine once, in place.
   The audit hook is a Python `sys.addaudithook` hook or the platform's equivalent. It allows writes only inside
   the project's gitignored scratch folder, or a `scratch/` folder inside the workspace when the project names
   none, and it checks each path after resolving it to its real path. It blocks every other write, every
   subprocess and every network call, unless the task brief names an exception the user approved. The log
   records every file write, subprocess call and network call, and marks the blocked ones.
   - Flag each blocked event, and each allowed write the task didn't ask for. Flag a missing log for such a
     script too.
   - A non-Python script, or one that needs arguments, doesn't run, and the gate marks it "side effects not
     checked". Record each such script as a documented limit.
4. **The ship list.** The gate listed what would ship. That is the tracked files plus the untracked files that
   aren't ignored. Flag any listed file that must not ship. That
   covers secrets, `.env` files, local paths, scratch output, eval results, private notes and the term list
   itself. Flag a missing ship list too.
5. **Trust boundaries.** External content covers web pages, fetched files, third-party repos and tool output.
   Wherever the change's code or docs read such content, they must read it under web-trust-policy. They name
   that skill and don't copy its text. Flag each place that reads external content without it.

## Mode: round 1

Read these files:
- the review package: `<package path>`
- the task brief: `<task brief path>`
- the implementer's report: `<implementer report path>`
- the decided items, which are the ledger's rulings and the user's decisions: `<decided items path>`
- the gate's ship list: `<ship list path>`
- the gate's audit log: `<audit log path>`
- the drafted commit message, plus the PR body on a branch's last task: `<hand-off text path>`
- the term list the project's CLAUDE.md names: `<term list path>`

The hand-off text goes public, so check it under areas 1 and 2. Treat each decided item as settled. Write the
report to `<report path>`.

## Mode: round 2

Read these files:
- the seat's own round-1 report: `<report path>`
- the diff since round 1: `<round-2 diff path>`
- the decided items, including any user decision made since round 1: `<decided items path>`
- the term list the project's CLAUDE.md names: `<term list path>`
- the drafted hand-off text, when it changed since round 1: `<hand-off text path>`
- the rebuilt ship list, when the gate rebuilt it: `<ship list path>`
- the rebuilt audit log, when the gate rebuilt it: `<audit log path>`
<!-- Build the diff since round 1 with git diff --no-index <round-1 package> <round-2 package>. -->

Give each round-1 blocking finding its verdict, as the contract's Round 2 section describes. Review only the text
the diff changes, and check that text against the term list. Check any changed hand-off text under areas 1 and 2,
and any rebuilt ship list or audit log under areas 3 and 4. Only the contract's finding classes block. Append the
Round 2 section to `<report path>`.
