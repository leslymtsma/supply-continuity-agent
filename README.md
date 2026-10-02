# ERP Exception Agent — Supplier Delay Triage

An AI agent that turns a supplier delay into a decision: it finds the purchase orders that are late, traces which production and customer orders are at risk, looks for alternatives, and proposes an action. **A human approves every change.**

Built in public by Lesly Matsouma, from operations to agentic AI. Every step is dated in the commit history and in [`JOURNAL.md`](JOURNAL.md).

## The business problem

In manufacturing, a supplier slipping by two weeks is routine. What hurts is finding out too late which customer orders it affects. Today, a planner checks this by hand across several ERP screens. This project shows how an agent can do the legwork and leave the decision to the planner.

## Status

| Stage | Status |
|---|---|
| Day 1 — Script that lists delayed purchase orders | 🟡 in progress |
| Mini-ERP in SQLite + impact chain | ⚪ planned |
| MCP server: agent reads and proposes | ⚪ planned |
| Demo with human approval | ⚪ planned |
| Enablement kit for presales | ⚪ planned |

Full plan: [`ROADMAP.md`](ROADMAP.md)

## Project structure

```
data/       sample ERP data (fictional company and suppliers)
scripts/    Python scripts
docs/       data model, queries, guides (coming)
```

## Run it

```bash
python scripts/late_orders.py
```

## Note

All companies, suppliers and figures are fictional.
