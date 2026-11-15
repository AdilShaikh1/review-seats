<h1 align="center">Review Seats</h1>

<p align="center"><strong>Up to three review seats and a cost ceiling for every&nbsp;task&nbsp;an&nbsp;agent&nbsp;ships.</strong></p>

<p align="center">An agent skill for Claude Code, Codex and other coding agents.<br>It runs alongside subagent&#8209;driven&#8209;development, gives every task up to three review seats, hands each git write to the user and caps the cost of review.</p>

<p align="center"><a href="#what-it-does">What&nbsp;it&nbsp;does</a>&nbsp;&nbsp; &nbsp;&nbsp;<a href="#install">Install</a>&nbsp;&nbsp; &nbsp;&nbsp;<a href="#usage">Usage</a>&nbsp;&nbsp; &nbsp;&nbsp;<a href="#common-questions">Common&nbsp;questions</a>&nbsp;&nbsp; &nbsp;&nbsp;<a href="#license">License</a></p>

## What it does

The skill changes how `superpowers:subagent-driven-development` runs, so it needs the superpowers plugin installed. Every task gets an overseer for logic and correctness and a code-quality seat. A security seat joins when the task touches a security trigger, for example untrusted input, network calls, file writes or public docs. The seats section below names every trigger. The agent never runs a git write. Each task ends with a summary and a PowerShell command block for the user to run. A two-round stop rule, a local gate before any seat round and a cap of 4 agents at once keep each task's review cost bounded.

## When to reach for it

Ask for it by name ("run the review seats", "review this branch with agents"), or the agent reaches for it on its own whenever it runs subagent-driven-development, executes a plan with subagents, or starts any other multi-agent review, including a review round before a release.

A seat that reads external content calls `web-trust-policy` by name, and prose goes through `writing-voice`. `deep-pr-review` runs only when the user asks for it.

## Install

**Requirements.** The skill needs the superpowers plugin and the ponytail plugin. The code-quality seat calls the ponytail-review and ponytail skills and has no fallback. In Claude Code, install ponytail with `/plugin marketplace add DietrichGebert/ponytail`, then `/plugin install ponytail@ponytail`. In Codex, run `codex plugin marketplace add DietrichGebert/ponytail`, then `codex plugin add ponytail@ponytail`.

As a Claude Code plugin, add this repo as a marketplace and install from it:

```text
/plugin marketplace add AdilShaikh1/review-seats
/plugin install review-seats@review-seats
```

For Codex, Cursor and other agents, run `npx skills add AdilShaikh1/review-seats -g`. It uses the [skills](https://github.com/vercel-labs/skills) CLI from npm.

To edit the skill and have changes apply at once, clone the repo and link the skill folder into your skills directory. Use your own clone folder in place of `<clone-dir>`. The first block is for macOS or Linux. On Windows, the second block makes a directory junction, which needs no admin rights.

```bash
git clone https://github.com/AdilShaikh1/review-seats <clone-dir>
mkdir -p ~/.claude/skills && ln -s <clone-dir>/skills/review-seats ~/.claude/skills/review-seats
```

```powershell
git clone https://github.com/AdilShaikh1/review-seats <clone-dir>
New-Item -ItemType Directory -Force $HOME\.claude\skills
New-Item -ItemType Junction -Path $HOME\.claude\skills\review-seats -Target <clone-dir>\skills\review-seats
```

## Usage

Take a branch that adds a CSV export feature. The user runs a block with `git switch main`, `git pull` and `git switch -c feature/csv-export`, then says "done".

The implementer writes the change on that branch and leaves it uncommitted. The local gate runs first. It runs every self-test and regression check the project has. The seats run next, for at most two rounds. An overseer and a code-quality seat run on every task, plus a security seat when a trigger such as file writes or public docs applies.

Once the review is clean, or every remaining finding is a documented limit, the agent gives the user a short summary of the change and one command block. The summary names every documented limit and any skipped security seat, each with its reason.

```powershell
git add src/export.py tests/test_export.py
git diff --cached --stat   # expect 2 files changed
git commit -m 'Add CSV export for the report table'
```

The user runs the block and says "done". The next task starts from that commit.

On the branch's last task, the overseer's whole-branch review runs first, before that task's block. The block then adds `git push -u origin feature/csv-export` and `gh pr create`.

## The seats

**Overseer.** It checks logic and correctness, and whether the docs match the code, under the domain persona the project's CLAUDE.md names. It runs on every task, on the most capable model, and again over the whole branch before the last task's command block. Template: [skills/review-seats/references/overseer.md](skills/review-seats/references/overseer.md).

**Code quality.** It calls the ponytail-review and ponytail skills by name and reviews the diff against them, with no fallback. It runs on every task. Template: [skills/review-seats/references/code-quality.md](skills/review-seats/references/code-quality.md).

**Security.** It runs when the task touches untrusted input, secrets or term lists, network or subprocess calls, file writes, what ships, public docs, dependency or lock-file changes, CI workflows, git hooks, or permission or settings changes. The ledger records the reason when it's skipped. Template: [skills/review-seats/references/security.md](skills/review-seats/references/security.md).

All three seats share one contract for their ground rules, the verdict format and the classes of finding that block. It lives at [skills/review-seats/references/brief-contract.md](skills/review-seats/references/brief-contract.md).

## Critical areas

A critical area is a part of a project where a short diff can silently break correctness. A three-line change to a quaternion's component order still runs, yet every pose that passes through it comes out wrong.

Code quality and security run on the mid tier for a diff under about 150 lines whose only security triggers are public docs or what ships. A diff that touches a listed critical area goes to the most capable model at any size. The overseer always runs there. Its brief names each critical area the diff touches, so the overseer checks those first. The ledger records which areas each diff touches, or that it touches none.

A project lists its critical areas in its own CLAUDE.md, next to the overseer persona and the term list. Each area gets one bullet that names it, says what it covers and says what can silently break:

```markdown
## Critical areas

- **Coordinate frames and transforms** (`src/geometry/`). Extrinsics, quaternion
  component order, transform direction. An inverted transform still runs and moves
  every point by the wrong pose.
- **Data splits and leakage.** Train, validation and test boundaries. A scaler fitted
  before the split inflates validation scores, and nothing fails.
```

[skills/review-seats/references/critical-areas.md](skills/review-seats/references/critical-areas.md) holds example lists for 3D perception and for machine learning and data science. Copy them and trim them to the project.

## Common questions

**Why doesn't the agent commit?** The user keeps every git write. The agent writes the change, and the user runs the add, commit, push and PR steps from a command block.

**Why two rounds?** More rounds cost more agent time per task. The stop rule still fixes a leak, a regression or a broken doc promise found later, just without a full round around it.

**What happens to a finding after round 2?** A leak, a regression or a broken doc promise is fixed on its own. Only the seat that raised it checks the fix. Every other finding becomes a documented limit in the ledger, with its own one-line wording.

**Is ponytail required?** Yes. The code-quality seat calls the ponytail-review and ponytail skills by name and has no fallback. When they aren't installed, the agent stops before the first seat round and asks for them.

## It's working if

- Every task's summary names its documented limits and any skipped security seat, each with a reason, before the user runs anything.
- The agent never runs a git write. Every add, commit, push and PR step comes from a command block the user runs.
- A task gets at most two seat rounds. After that, a leak, a regression or a broken doc promise is fixed on its own,
  and only the seat that raised it checks the fix.
- The agent stops and asks before the first seat round when ponytail or ponytail-review isn't installed.

## Tests

`skills/review-seats/evals/` holds four test cases in the folder layout that `claude plugin eval` reads. One fills the three seat briefs from their templates, one decides when the security seat runs and which model tier each seat gets, and one triages findings under the stop rule. The fourth checks that a short diff in a critical area goes to the most capable model. Each case has a prompt, fixture files and a set of graders. Each case's `place-files.sh` copies its fixtures into the sandbox folder `claude plugin eval` runs it in, so the run stays inside that sandbox. On Windows, run them from Git Bash or WSL, because the scaffold scripts need bash. Run them with:

```bash
claude plugin eval skills/review-seats --scaffold --allow-tools Write,Edit,Bash
```

These are real, billed model runs on the same model. On the four cases, the skill passes 32 of 32 assertions and the agent without it passes 17 of 32. The critical-area case alone goes from 3 of 7 without the skill to 7 of 7 with it.

## Repository layout

- `skills/review-seats/SKILL.md`: the skill
- `skills/review-seats/references/`: the seat brief templates, the contract they share and the critical-areas examples
- `skills/review-seats/agents/openai.yaml`: the skill's name and description for Codex
- `skills/review-seats/evals/`: test cases
- `.claude-plugin/`: the plugin and marketplace manifests
- `.agents/adr/`: decision records
- `CLAUDE.md` and `AGENTS.md`: rules for agents working on this repo
- `CHANGELOG.md`: the release notes
- `README.md`, `LICENSE` and `.gitignore`: this page, the MIT license and the files git leaves out

## License

This repo is MIT licensed (see LICENSE). Issues are welcome. Please describe what you ran, what the skill did and what you expected.
