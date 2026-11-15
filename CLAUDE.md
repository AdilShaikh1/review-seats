# Working on this repo

This repo holds one skill, `review-seats`, in `skills/review-seats/`. It is public, so everything in it is written for strangers to read.

## Layout

The README's Repository layout section lists every folder and file this repo holds.

## When something changes

- A change to this repo goes through review-seats itself. The agent runs it alongside subagent-driven-development, with its own seats and gates, on this repo's own branches.
- The local gate is a project-local script. It lives in this repo's gitignored `INTERNAL/` folder and ships with nothing the skill distributes.
- When the skill's behaviour text or a fixture changes, rerun the evals in `skills/review-seats/evals/` with `claude plugin eval`, plus one final pass on the release candidate.
- A decision that shapes the repo gets a short record in `.agents/adr/`.
- The "up to three" seat-count wording reads the same in SKILL.md, the README and the three manifests (plugin.json, marketplace.json, openai.yaml).

## Before every commit

- Use no em dashes in any prose, except a fixture ledger's identity line, which copies subagent-driven-development's own format.

## Public safety

- Fixtures under `evals/*/files/` are test data. Their `CLAUDE.md` files and term lists are made up, not real project rules or real confidential terms.
- No real employer, client, interviewer, colleague or private figure appears anywhere, including test fixtures.
