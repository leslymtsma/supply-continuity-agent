# Project instructions for Claude Code

## Context
This repo is the Supply Continuity Agent, a learning-by-building project: an agent that triages supplier delays on ERP-like data (fictional manufacturer). The owner, Lesly, comes from operations and project management and is learning Python, SQL and MCP while building it.

## How to work with me
- Explain every change in plain English: what it does and why, in a few sentences.
- Keep the code simple and readable. Prefer the Python standard library. No clever one-liners, no unnecessary classes.
- Add short comments that explain the business logic ("why"), not the syntax.
- When I say "I want to try first", give me hints, not the solution.
- Never delete or overwrite files in `data/` without asking.
- Small steps: one feature at a time, then let me run it.

## Conventions
- Everything in English: code, comments, commit messages, docs.
- Python 3.12+. Scripts live in `scripts/`, data in `data/`, docs in `docs/`.
- Commit messages: short, imperative ("Add delay summary by supplier").

## Design rules
- Quantities, dates, shortages and costs are computed in code (deterministic). The language model is not trusted to calculate them.
- Hard constraints (decision-policy.md) are enforced in code. The model can challenge the preference ranking, never a hard constraint.
- The agent reads operational data and writes only to the proposals and audit log. Only the planner approves an operational change.
- Tools are plain Python functions first; API tool use and MCP are adapters over the same functions.
- Before designing anything new, read README.md, docs/architecture.md, docs/scenarios.md and docs/decision-policy.md.
