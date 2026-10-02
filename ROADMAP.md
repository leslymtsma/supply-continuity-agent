# Roadmap — 18 days from zero to a working agentic ERP demo

**Goal:** build, in public and with a dated history, an AI agent that handles a real manufacturing exception (a supplier delay) on top of ERP-like data, connected through MCP, with a human approving every action, and package it so non-technical presales people can reuse it.

**Time budget:** 4–6 h/day. The daily routine below assumes 5 h.

## Daily routine

| Block | Time | What |
|---|---|---|
| Learn | 1 h | One course module (Claude Academy) or one reading |
| Build | 3 h | Code with Claude Code. Try first, ask second |
| Market watch | 30 min | ERP / agentic AI news, vendor release notes, job ads |
| Log & ship | 30 min | Write the day in `JOURNAL.md`, commit, push |

From **Day 5** on, add **1 h/day of targeted applications** (presales ERP/SaaS, AI consulting, internal AI transformation roles), linking to this repo.

## Three rules

1. **Explain every file.** If I can't say what a function does in one sentence, I'm not done. After each session: ask Claude Code to explain the code, then rewrite the explanation in my own words in the journal.
2. **Log every failure.** Each error and how I fixed it goes in the journal. These are my interview answers.
3. **Ship daily.** At least one commit per day, even a small one. The commit history is the proof.

---

## Phase 1 — Foundations (Days 1–2)

### Day 1 — Set up and write my first script
- **Course:** Claude Code 101 (Claude Academy)
- [ ] Install VS Code, Git, Python, GitHub CLI, Claude Code
- [ ] Create the GitHub repo, clone it, add the starter files, push the first commit
- [ ] Python basics: variables, lists, dictionaries, `for`, `if`, functions
- [ ] Write `scripts/late_orders.py` (version 1 by hand, version 2 with Claude Code)
- [ ] First journal entry
- **Deliverable:** a script that lists delayed purchase orders with their days of delay

### Day 2 — Make it solid
- **Course:** finish Claude Code 101, start Claude Code in Action
- [ ] Refactor the script into small functions (`load_orders`, `is_delayed`, `delay_days`)
- [ ] Add a summary by supplier (number of delayed POs, average delay)
- [ ] Add a command-line option: `--min-days 7`
- [ ] Learn to read a Python error message (traceback): break the script on purpose 3 times and fix it
- [ ] Write a `CLAUDE.md` rule I learned the hard way
- **Deliverable:** a clean, explained script plus 3 logged errors

## Phase 2 — Understand the ERP by building one (Days 3–5)

### Day 3 — Business processes and data model
- **Reading (1 h):** order-to-cash, procure-to-pay, MRP, bill of materials (BOM), routing, work orders, lot traceability
- [ ] Draw the data model in `docs/data-model.md` (Mermaid diagram): items, BOMs, suppliers, purchase orders, inventory, work orders, sales orders, customers
- [ ] Write each process in 5 lines, in business language
- **Deliverable:** a data model I can explain to a supply chain manager

### Day 4 — Build the mini-ERP in SQLite
- **Course:** Claude Code in Action (continue)
- [ ] `scripts/seed_db.py`: create the tables and load realistic fictional data
- [ ] SQL basics: `SELECT`, `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY`
- [ ] 5 business questions answered in SQL (saved in `docs/queries.sql`)
- **Deliverable:** `erp.db` generated from scratch with one command

### Day 5 — The impact chain
- [ ] `scripts/impact.py`: from one delayed PO → affected work orders → affected sales orders → customers at risk and revenue at stake
- [ ] Check inventory and alternative suppliers for the delayed item
- [ ] Explain the chain in business terms in the README
- **Deliverable:** "This delay puts €X of orders for customer Y at risk." Computed, not guessed.
- **Start:** 1 h/day of applications. **LinkedIn post #1:** what I'm building and why.

## Phase 3 — MCP: let the agent act (Days 6–9)

### Day 6 — MCP fundamentals
- **Course:** Introduction to Model Context Protocol
- [ ] Concepts: host / client / server; tools / resources / prompts; stdio vs HTTP transport
- [ ] Install `uv`, create a "hello world" MCP server with one tool
- [ ] Connect it to Claude Desktop and call it from a chat
- **Deliverable:** a working MCP server, plus my first debugging story in the journal

### Day 7 — Read tools
- [ ] `get_delayed_purchase_orders(min_days)`
- [ ] `get_impact(po_id)`: work orders, sales orders, customers, revenue at risk
- [ ] `find_alternatives(item_code)`: stock, other suppliers, lead times
- [ ] Clear tool descriptions (they are prompts for the model)
- **Deliverable:** Claude answers "Which delays hurt us most this week?" from live data

### Day 8 — Action tools with a human in the loop
- [ ] `propose_action(...)`: writes a proposal (reallocate stock, switch supplier, reschedule) with status `pending`
- [ ] `draft_supplier_email(po_id)`
- [ ] `approve_proposal(id)`: the only tool that changes data, and only after explicit human approval
- [ ] Log every agent action in an `audit_log` table
- **Deliverable:** an agent that proposes, a human who decides, a trace of everything

### Day 9 — Demo v0
- [ ] Full scenario from start to finish, 3 times without errors
- [ ] Record a 2-minute video
- [ ] README: screenshots, how to run it, architecture diagram
- **Deliverable: demo v0. This is what I show the hiring manager.**

## Phase 4 — Demo engineering (Days 10–12)

### Day 10 — The agent loop without Claude Desktop
- **Course:** Building with the Claude API (tool use chapters only)
- [ ] A Python script that calls the Claude API with the same tools: understand the loop (request → tool call → result → answer)
- [ ] Note the cost of a full run (it should be cents)
- **Deliverable:** I can explain what Claude Desktop does for me behind the scenes. **LinkedIn post #2.**

### Day 11 — A visual widget
- [ ] A small exception dashboard (Streamlit or a single HTML page): delayed POs, impact, agent recommendation, "Approve" button
- **Deliverable:** something a business user understands in 10 seconds

### Day 12 — Polish
- [ ] Error handling and guardrails (unknown PO, missing data, refused approval)
- [ ] Demo talk track: problem → impact → agent → human decision → value (KPI)
- [ ] Demo v1 video, 2 minutes
- **Deliverable:** demo v1, presentation-ready

## Phase 5 — Enablement and automation (Days 13–15)

### Day 13 — Package the know-how
- **Course:** Introduction to Agent Skills
- [ ] A Skill `supplier-delay-triage` that packages the procedure
- [ ] A library of 5 reusable prompts for presales (`docs/prompt-library.md`)
- **Deliverable:** someone else can run my agent's logic without me

### Day 14 — Enablement kit
- [ ] One-page guide "Reuse this demo" for non-technical solution consultants
- [ ] FAQ: security, hallucinations, data access, what the agent is NOT allowed to do
- [ ] 5-minute onboarding video
- **Deliverable:** the "community enablement" part of the job, proven

### Day 15 — RPA vs agents
- [ ] One classic RPA automation (Power Automate Desktop or equivalent): e.g. copy approved proposals into a spreadsheet form
- [ ] `docs/rpa-vs-agents.md`: when to use rules, RPA, or an agent, with one example of each
- **Deliverable:** I can talk about RPA from experience. **LinkedIn post #3.**

## Phase 6 — Rehearse (Days 16–18)

### Day 16 — The AI talk (10 minutes)
- [ ] Topic: "Where agents create value in an industrial order-to-cash, and where they must not act alone"
- [ ] Value, governance, human scope, metrics
- **Deliverable:** deck + talk rehearsed twice, timed

### Day 17 — Demo and Q&A
- [ ] Demo rehearsed 3 times, timed; backup video if live fails
- [ ] Bank of 20 likely questions with answers (MCP debugging, security, ROI, limits, why this architecture)
- **Deliverable:** no question I haven't heard before

### Day 18 — Mock interviews
- [ ] Three mock interviews with Claude playing hiring manager, technical expert and VP
- [ ] Final README pass
- **Deliverable:** ready

---

## If an interview comes early

Fast path to a demo in ~7 days: Day 1 → Day 3 + 4 compressed (1 day) → Day 5 → Days 6–8 → record v0. Skip the rest until after the interview.
