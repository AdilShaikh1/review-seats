# Changelog

## 0.1.0 (2026-11-15)

- The first release holds the review-seats skill for `superpowers:subagent-driven-development`, with up to three
  review seats per task (overseer, code quality, security), their brief templates and a shared brief contract.
- Every git write goes to the user as a command block.
- The user creates each branch before its work starts.
- The cost gates include a two-round stop rule, a local gate before any seat round, model tiers by risk and size,
  and a cap of 4 agents at once.
- A project can list its critical areas, the parts of its code where a short diff can silently break correctness.
  The skill ships example lists for 3D perception and for machine learning and data science.
- Four controller eval cases ship with the skill. With the skill, 32 of 32 assertions pass. Without it, 17 of 32
  pass. The README's Tests section gives the setup.
