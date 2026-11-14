---
name: review-seats
description: >
  Review seats and cost gates for superpowers:subagent-driven-development. Load it alongside that skill whenever the
  agent runs subagent-driven-development or executes a plan with subagents. Load it for any multi-agent review or
  review round before a release too, even when the user doesn't name it. Trigger phrases include "use
  subagent-driven development", "execute this plan with subagents", "review this branch with agents", "run the
  review seats" and "review before the release".
license: MIT
---

# Review seats

This skill tunes `superpowers:subagent-driven-development` (SDD) for a user who keeps every git write, wants small
PRs and pays for each review round. It adds up to three review seats to each task and a set of cost gates.

## 1. What it changes and why

SDD stays the engine. Call the Skill tool with "superpowers:subagent-driven-development" and follow it, except where
this skill overrides it. This skill never copies SDD's text or edits its files.

The overrides, by section:
- **Git hand-off (2)** replaces SDD's commits, its worktree setup and its finishing step. The agent also pauses
  after each task until the user has run the command block, in place of SDD's continuous execution.
- **Branches (3)** set the size and name of each branch and PR.
- **Rulings (4)** narrow SDD's "rulings, not stalls".
- **The seats (5)** replace SDD's single task reviewer, and the overseer runs the final whole-branch review.
- **The seat brief (6)** sets what each seat receives.
- **Cost gates (7)** replace SDD's five-round fix loop with a two-round stop rule and add a local gate, an eval
  rule, an agent cap and one-line progress updates.

Everything else in SDD still applies, including its ledger, task briefs, report files and model naming on every
dispatch.

A review with no plan, such as "review this branch" or a pre-release review, skips SDD. It shows the user the
`git status --porcelain --untracked-files=all` output as section 2 does, runs the local gate, then runs the seats
from sections 5 to 8 under the same stop rule. Their package is `git diff main...HEAD`, plus `git diff HEAD` for
uncommitted tracked edits, plus the untracked files.

## 2. Git hand-off (overrides SDD's commits)

The agent never runs a git write. That covers add, commit, branch, push, merge, tag and PR creation, and it
includes SDD's worktree setup. Implementer dispatches leave out the template's commit step. Read-only git such as
status, diff, log and show stays allowed.

Before a task starts, the agent runs `git status --porcelain --untracked-files=all`, which lists each file inside
a new folder. When the output isn't empty, the agent shows it to the user first. The agent then records BASE in
the ledger. BASE is `git rev-parse HEAD`. Each task runs in this order:

1. The implementer writes the change and leaves it uncommitted.
2. The local gate runs (section 7) and comes back clean.
3. The seats review the uncommitted change against BASE.
4. Fix rounds run, with at most two seat rounds in total. On a branch's last task, the overseer's whole-branch
   review (section 5) runs next, and any fix it needs joins this task's change.
5. The user gets a short summary of the change and one command block. The summary lists every documented limit
   and any skipped security seat, each with its reason. It also names each script the gate marked "side effects
   not checked" and each audit-hook exception the user approved.
6. The user runs the block and says "done".
7. The next task starts from that commit.

The command block comes once per task, once the review is clean or every remaining finding is a documented
limit. Fix rounds produce no blocks. Push and `gh pr create` sit in the last task's block, so they run only after
the whole-branch review is clean.

The review package uses read-only git. It holds `git diff BASE` for tracked changes plus the full text of each new
file that `git ls-files --others --exclude-standard` lists, written to one file in the workspace. Each round's
package is its own file, `task-N-package-rK.md`, and no package file is overwritten. Round 2 diffs the two
packages. SDD's `review-package` script compares two commits, so it doesn't fit an uncommitted change. A repo's
first commit has no BASE, so its seats read the files directly.

The command block is PowerShell and holds these steps in order:

1. `git add` with each file named. Never `git add .`.
2. `git diff --cached --stat`, with the expected file count stated beside it.
3. `git commit -m '<short subject>'`. The message never mentions AI or the agent.
4. On a branch's last task, `git push -u origin <branch>` and `gh pr create` with a body that says what changed
   and how it was verified.

Commit and PR text goes in single-quoted PowerShell strings. That text never contains a quote mark of either kind, a
backslash, `$` or a backtick, because Windows PowerShell 5.1 splits a native command's argument at an inner double
quote.

A task's block on the branch `feature/csv-export` looks like this:

```powershell
git add src/export.py tests/test_export.py
git diff --cached --stat   # expect 2 files changed
git commit -m 'Add CSV export for the report table'
```

The last task on that branch adds two lines:

```powershell
git push -u origin feature/csv-export
gh pr create --title 'Add CSV export' --body 'Adds CSV export for the report table. Verified with the export tests and one real export run.'
```

A new repo's first commit gets its own block with no branch and no PR. That block runs `git init -b main` (skipped
for a cloned empty repo), the named `git add`, the `--stat` check and `git commit`. It ends with
`gh repo create <owner>/<name> --<public|private> --source . --remote origin --push`, with the visibility the
user chose, or `git push -u origin main` when the remote already exists. Before that commit exists, the agent
creates the workspace folder by hand.

## 3. Branches

- Before a branch's first task, the agent gives the user a block with `git switch main`, `git pull` and
  `git switch -c feature/<2-3 words>`. It waits for "done" before it records BASE. All the work then happens
  uncommitted on that local branch. After a new repo's first commit, no work happens on main.
- One feature per branch, with about 400 changed lines at most. Generated files, fixtures and lock files don't
  count toward that size.
- Name the branch `feature/<2-3 words>` when there is no ticket. When a ticket exists, use its key form, such as
  `feature/ABC-124-csv-export`.
- Each branch gets one PR. The user creates and merges it, and the next branch starts from the updated main.
- A new repo's first commit holds only its core building block. Everything else arrives in later branches.

## 4. Rulings (narrows SDD's "rulings, not stalls")

SDD tells the agent to decide conflicts and ambiguities and keep going. This skill narrows what the agent may
decide alone.

- Small implementation choices inside the agreed plan are the agent's to decide. Each one goes in the ledger as a
  ruling with its reason.
- Design choices, formulas, root causes and anything outside the plan go to the user. The agent batches them into
  one round of questions instead of asking one at a time.

## 5. The seats

The overseer and the code-quality seat run on every task. The security seat runs when one of its triggers
applies. Each seat writes its own report into the workspace (section 8).

1. **Overseer.** It checks logic and correctness, and whether the docs match the code. It takes the domain
   persona the project's CLAUDE.md names. Without one, it reviews as a senior engineer for the project's stack.
2. **Code quality.** It calls the ponytail-review and ponytail skills by name with the Skill tool and reviews the
   diff against them. Both skills are required, and the seat has no fallback. They come from the ponytail plugin
   (`DietrichGebert/ponytail`). When they aren't installed, the agent stops before the first seat round and asks
   the user to install them.
3. **Security.** It runs when the task touches any of these:
   - untrusted input
   - secrets or term lists
   - network or subprocess calls
   - file writes
   - what ships
   - public docs
   - dependencies or lock files
   - CI workflows
   - git hooks
   - permissions or settings

   When none of these applies, the ledger records why the security seat was skipped.

Models for the seats:
- The overseer always runs on the most capable model.
- Code quality and security run on the mid tier for a diff under about 150 lines with low risk. Otherwise they
  run on the most capable model.

The final review is the overseer reading the whole branch, on the most capable model. It uses the whole-branch
mode of [references/overseer.md](references/overseer.md), which also sets its fix cap. It replaces SDD's final
reviewer. On a branch's last task, it runs after that task's seats and before its command block, and any fix it
needs goes into that same block. deep-pr-review runs only when the user asks for it.

## 6. The seat brief

Each seat gets a brief handed over as file paths and never as pasted text. The agent copies the seat's template
into the workspace, keeps the mode that applies and fills in the paths.
- The overseer uses [references/overseer.md](references/overseer.md).
- The code-quality seat uses [references/code-quality.md](references/code-quality.md).
- The security seat uses [references/security.md](references/security.md).

All three templates point to [references/brief-contract.md](references/brief-contract.md). It holds the ground
rules, the finding classes and the verdict format.

## 7. Cost gates

**The stop rule.** A task gets at most two seat rounds. In rounds 1 and 2, a finding blocks when it is a leak, a
regression, a broken doc promise or a should-fix defect. A should-fix defect is wrong behaviour, a contradiction,
or a rule an agent can't follow as written. After the second round, a leak, a regression or a broken
doc promise is still fixed on its own. Only the seat that raised it checks the fix, so no full round runs. A
security vulnerability counts as a leak. Every other finding becomes a documented limit in the ledger.

**The local gate.** Before any seat round, the agent runs every self-test, mutation and regression check the
project has, on each target runtime. It also reruns every earlier probe set and compares the result with the
previous version. That comparison lists anything the new version now misses. When the security seat runs, the
gate also lists what would ship with `git ls-files` plus `git ls-files --others --exclude-standard`. It runs each
new or changed script that runs on the user's machine once, in place, under an audit hook. The hook allows writes
only inside the project's gitignored scratch folder, or a `scratch/` folder inside the workspace when the project
names none, and it checks each path after resolving it to its real path. It blocks every other write, every
subprocess and every network call, unless the task brief names an exception the user approved. A script the hook
can't watch, such as a non-Python script or one that needs arguments, doesn't run, and the gate marks it "side
effects not checked". The gate saves these outputs in the workspace, outside `scratch/`, as the security template
describes. Before the seats start, the agent also saves the drafted commit message there, plus
the PR body on a branch's last task. Seats start only when the gate is clean.

**Evals.** Evals rerun only when the skill's behaviour text or a fixture changes. One final pass runs on the
release candidate. Both run on the session's own model.

**Concurrency.** At most 4 agents run at once. When seats and evals both wait, the seats go first.

**Progress updates.** The agent posts one line per agent hand-back.

## 8. Where things live

SDD's workspace under `<repo>/.superpowers/sdd/` holds the ledger, the briefs, the seat reports and the gate
results. Before the first task, confirm that `.gitignore` covers `.superpowers/`, so nothing review-related ships.
When it doesn't, add the line and name `.gitignore` in the first command block.

## 9. Works with

- `superpowers:subagent-driven-development`, the engine this skill tunes
- `ponytail-review` and `ponytail`, which the code-quality seat requires
- `web-trust-policy`, for any external content a seat reads
- `writing-voice`, for prose
- `deep-pr-review`, only when the user asks for it
