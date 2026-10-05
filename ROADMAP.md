# Roadmap — 18 days to a working Supply Continuity Agent

**Goal:** build, in public and with a dated history, an agent that turns a supplier delay into a structured decision on top of ERP-like data. It investigates through bounded tools, its path depends on what it discovers, numbers stay deterministic, and a planner approves every operational change. Then package it so non-technical presales people can reuse it.

**Time budget:** 4–6 h/day. The daily routine below assumes 5 h.

## Daily routine

| Block | Time | What |
|---|---|---|
| Learn | 1 h | One course module (Claude Academy) or one reading |
| Build | 3 h | Code with Claude Code. Try first, ask second |
| Market watch | 30 min | ERP / agentic AI news, vendor release notes, job ads |
| Log & ship | 30 min | Write the day in `JOURNAL.md`, commit, push |

From **Day 5** on, add **1 h/day of targeted applications** (presales ERP/SaaS, AI consulting, internal AI transformation roles), linking to this repo.

## Five rules

1. **Explain every file.** If I can't say what a function does in one sentence, I'm not done.
2. **Log every failure.** Each error and its fix goes in the journal. These are my interview answers.
3. **Ship daily.** At least one commit per day. The history is the proof.
4. **Numbers are deterministic.** Quantities, dates and costs come from code; the model receives them as inputs.
5. **Define "good" before measuring.** Golden files first, agent second.

---

## Phase 1 — Foundations (Days 1–2)

### Day 1 — Set up and react to a first delay event
- **Course:** Claude Code 101 (Claude Academy)
- [ ] Install VS Code, Git, Python, GitHub CLI, Claude Code
- [ ] Create the GitHub repo and push the first commit
- [ ] Python basics: variables, lists, dictionaries, `for`, `if`, functions, dates
- [ ] Write `scripts/affected_pos.py`: given a supplier and a delay in days, list the affected open POs with their new expected date, their lateness vs. the original promise, and the production order they feed (version 1 by hand, version 2 with Claude Code)
- [ ] First journal entry
- **Deliverable:** "Kesteren Motors is 14 days late" → the list of affected POs and production orders

### Day 2 — Make it solid
- **Course:** finish Claude Code 101, start Claude Code in Action
- [ ] Refactor into small functions (`load_orders`, `is_affected`, `new_expected_date`)
- [ ] Command-line arguments: `--supplier "Kesteren Motors" --delay-days 14`
- [ ] Handle edge cases: unknown supplier, no open PO, `partially_received`
- [ ] Learn to read a Python traceback: break the script on purpose 3 times and fix it
- **Deliverable:** a clean, explained script plus 3 logged errors

## Phase 2 — Model the business, define "good", compute the impact (Days 3–5)

### Day 3 — Data model, scenarios and golden files
- **Reading (1 h):** procure-to-pay, order-to-cash, MRP, bill of materials (BOM), production orders, available inventory
- [ ] Data model in `docs/data-model.md` (Mermaid diagram): suppliers, components, purchase orders, inventory, products, BOMs, production orders, customers, customer orders, alternate supply offers
- [ ] Finalize scenarios A, B and C with real numbers in `docs/scenarios.md`
- [ ] Write one golden file per scenario in `eval/golden/` (expected objects, figures, valid options, required and forbidden tools), **before** building anything
- **Deliverable:** I know what "correct" means before measuring anything

### Day 4 — Build the mini-ERP in SQLite
- **Course:** Claude Code in Action (continue)
- [ ] `scripts/seed_db.py`: create the tables and load scenarios A, B and C
- [ ] SQL basics: `SELECT`, `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY`
- [ ] 5 business questions answered in SQL (`docs/queries.sql`)
- **Deliverable:** `erp.db` rebuilt from scratch with one command

### Day 5 — The deterministic impact engine
- [ ] `engine/impact.py`: delay event → affected POs → production orders → shortage → customer orders at risk
- [ ] Option figures: alternate supplier, expedite, resequencing (feasible? extra cost? customer date?)
- [ ] Tests: the engine matches the golden figures for A, B and C (target 100%)
- **Deliverable:** "Shortage 280 units, SO-391 at risk, Supplier B feasible at +€X." Computed and tested.
- **Start:** 1 h/day of applications. **LinkedIn post #1.**

## Phase 3 — Tools, agent loop and governance (Days 6–9)

### Day 6 — Tool layer and tool use
- **Course:** Building with the Claude API (tool use chapters)
- [ ] Tools as plain Python functions over the engine: `get_affected_purchase_orders`, `get_dependent_production_orders`, `get_shortage`, `get_customer_orders_at_risk`, `find_alternate_supply`, `get_expedite_options`
- [ ] Clear descriptions and typed inputs/outputs: they tell the model when each tool is useful
- **Deliverable:** each tool callable and tested on its own

### Day 7 — The agent loop (direct API)
- [ ] A Python agent loop: request → tool call → result → next decision, until recommendation or step budget reached
- [ ] Decision policy passed to the model (`docs/decision-policy.md`)
- [ ] Recommendations must cite the IDs and tool outputs used
- **Deliverable:** the agent investigates scenario A on its own, and stops early in scenario B

### Day 8 — Governance and human approval
- [ ] Permissions: investigation tools read-only; `propose_action` writes only to `proposals`
- [ ] `approve_proposal(id)`: the only path to a change, triggered by the planner
- [ ] Step budget, `audit_log` table, safe degradation (disable a tool: the agent reports the gap instead of guessing)
- **Deliverable:** bounded, traced, and safe when something fails

### Day 9 — Demo v0 across three scenarios
- [ ] Run A, B and C; the agent takes three different paths
- [ ] Record a 2-minute video of scenario A (terminal transcript with visible tool calls is fine)
- [ ] Update the README status table and architecture with what really exists
- **Deliverable: demo v0. This is what I show the hiring manager.**

## Phase 4 — MCP, demo engineering and evaluation (Days 10–12)

### Day 10 — MCP adapter
- **Course:** Introduction to Model Context Protocol
- [ ] Concepts: host / client / server; tools / resources / prompts; stdio vs HTTP
- [ ] Expose the same tool functions through an MCP server (`uv`), connect it to Claude Desktop or Claude Code
- [ ] Journal: what MCP changed, what it did not, and why it is justified here
- **Deliverable:** the same agent capabilities, available in any MCP host. **LinkedIn post #2.**

### Day 11 — A visual widget and unstructured input
- [ ] Exception view (Streamlit or a single HTML page): event, impact chain, options, recommendation, Approve / Reject
- [ ] Input = a raw supplier email, parsed by the model into a structured event (then checked against the data)
- **Deliverable:** a planner understands the situation in 10 seconds

### Day 12 — V1 evaluation
- [ ] `eval/run_eval.py`: run A, B and C 5 times each, compare to golden files
- [ ] Publish V1 metrics in `docs/evaluation.md`: precision/recall, calculation accuracy, tool path rate, traceability, demo success rate, time to decision-ready insight vs. manual path
- [ ] Demo talk track: event → impact → options → recommendation → human decision
- **Deliverable:** demo v1 video, with measured results

## Phase 5 — Enablement and automation (Days 13–15)

### Day 13 — Package the know-how
- **Course:** Introduction to Agent Skills
- [ ] A Skill `supplier-delay-triage` that packages the investigation procedure
- [ ] A library of 5 reusable prompts for presales (`docs/prompt-library.md`)
- **Deliverable:** someone else can run the agent's logic without me

### Day 14 — Enablement kit and reusability test
- [ ] One-page guide "Reuse this demo" for non-technical solution consultants
- [ ] FAQ: security, hallucinations, data access, what the agent is NOT allowed to do
- [ ] Reusability test: someone who has never seen the repo runs a scenario without help (time to first demo, help requests, can they explain the narrative?)
- **Deliverable:** the "community enablement" part of the job, proven and measured

### Day 15 — Rules, RPA, workflow or agent?
- [ ] One classic RPA automation (Power Automate Desktop or equivalent)
- [ ] `docs/rules-rpa-workflow-agent.md`: when to use each, with one example from this project
- **Deliverable:** I can argue the choice of architecture. **LinkedIn post #3.**

## Phase 6 — Rehearse (Days 16–18)

### Day 16 — The AI talk (10 minutes)
- [ ] Topic: "Where agents create value in supply-chain exceptions, and where they must not act alone"
- **Deliverable:** deck + talk rehearsed twice, timed

### Day 17 — Demo and Q&A
- [ ] Demo rehearsed 3 times, timed; backup video if live fails
- [ ] Bank of 20 likely questions, including "Why is this an agent rather than a workflow with an LLM call?"
- **Deliverable:** no question I haven't heard before

### Day 18 — Mock interviews
- [ ] Three mock interviews with Claude playing hiring manager, technical expert and VP
- **Deliverable:** ready

---

## If an interview comes early

Fast path to a demo in ~7 days: Day 1 → Days 3–4 compressed (scenarios A and B only) → Day 5 → Days 6–8 → record v0. MCP, scenario C and the widget come after.
