<h1 align="center">Review Seats</h1>

<p align="center"><strong>Up to three review seats and a cost ceiling for every&nbsp;task&nbsp;an&nbsp;agent&nbsp;ships.</strong></p>

<p align="center">An agent skill for Claude Code, Codex and other coding agents.<br>It runs alongside subagent&#8209;driven&#8209;development, gives every task up to three review seats, hands each git write to the user and caps the cost of review.</p>

<p align="center"><a href="#what-it-does">What&nbsp;it&nbsp;does</a>&nbsp;&nbsp; &nbsp;&nbsp;<a href="#install">Install</a>&nbsp;&nbsp; &nbsp;&nbsp;<a href="#license">License</a></p>

## What it does

The skill changes how `superpowers:subagent-driven-development` runs, so it needs the superpowers plugin installed. Every task gets an overseer for logic and correctness and a code-quality seat. A security seat joins when the task touches untrusted input, secrets, network calls, file writes, what ships or public docs. The agent never runs a git write. Each task ends with a summary and a PowerShell command block for the user to run. A two-round stop rule, a local gate before any seat round and a cap of 4 agents at once keep each task's review cost bounded.

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

## License

This repo is MIT licensed (see LICENSE).
