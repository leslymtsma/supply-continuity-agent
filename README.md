# Supply Continuity Agent

**Supplier Delay Triage for Manufacturing**

A work-in-progress agentic AI prototype designed to turn a supplier delay into a structured decision.

The target system identifies affected purchase orders, traces downstream impact across inventory, production and customer commitments, evaluates available alternatives, and recommends a course of action. Every operational change requires human approval.

This project explores how agentic AI, APIs and deterministic business rules can reduce the investigative work behind supply-chain exceptions without removing planner accountability.

## The business problem

Recording a supplier delay can be straightforward. The harder question is understanding what it puts at risk downstream, early enough to act.

This project models a manufacturing scenario in which assessing a supplier delay requires a planner to correlate purchase orders, inventory, production demand and customer commitments before deciding on an appropriate response.

It explores how an AI-enabled workflow can perform that investigative work while keeping the final operational decision with the planner.

## How it works (target design)

**Incoming event:** *"Supplier X will be two weeks late"*, as structured data or as a free-text supplier message.

```
supplier delay event
  → identify affected open purchase orders
  → retrieve dependent production orders
  → check available inventory and compute shortages
  → retrieve affected customer orders
  → query alternate suppliers, stock and expedite options
  → evaluate options against a defined decision policy
  → recommend an action and wait for planner approval
```

**Why an agent rather than a fixed workflow with an LLM call.** The investigation path depends on the evidence. If inventory covers the shortage, the agent stops and reports that no action is needed. If not, it looks for alternate supply. If no alternative arrives in time, it examines production resequencing or a customer date change. Within defined permissions, the agent determines which investigative tools to call next based on the evidence returned by previous steps. The workflow is therefore dynamic, but the actions available to the agent remain explicitly bounded.

Three scenarios in [`docs/scenarios.md`](docs/scenarios.md) are designed to require three different investigation paths. Layers, permissions and the MCP rationale are in [`docs/architecture.md`](docs/architecture.md).

## Where AI is used, and where it is not

| Function | Handled by | Why |
|---|---|---|
| Resolve PO → component → production order → customer order links | Deterministic code | Structured relational query |
| Compute shortages, dates, quantities, costs | Deterministic code | Arithmetic must be exact and auditable |
| Check hard constraints (arrival before required date, budget ceiling, blocked supplier, sufficient quantity) | Deterministic code | Non-negotiable; an option that fails is not eligible |
| Rank eligible options by scored preferences | Deterministic code | Explicit, reproducible baseline |
| Parse a free-text supplier message into a structured event | AI | Unstructured language |
| Decide which information to investigate next | AI (agent) | The path depends on intermediate evidence |
| Interpret structured options and explain their trade-offs | AI | Contextual synthesis |
| Surface contextual considerations the score does not capture | AI | Qualitative information, e.g. in a supplier message |
| Recommend an option against defined business priorities | AI, bounded by the [decision policy](docs/decision-policy.md) | Reasoned, cited recommendation |
| Final decision, including any exception to the ranking | Planner | Operational and financial accountability |

The language model is not trusted to calculate operational quantities, dates or shortages. Those values are produced by deterministic tools and passed to the model as structured inputs.

The agent may challenge the deterministic ranking when additional qualitative context is available, but it cannot override hard constraints or execute the alternative. It must explain the discrepancy and escalate the decision to the planner.

## Design constraints

- Operational systems are read-only to the agent. The agent may write only to its proposal store and audit log. It cannot directly modify purchase orders, inventory, production plans or customer commitments.
- It can only call an explicit allowlist of tools.
- Each investigation has a configurable tool-call budget to prevent uncontrolled loops. The initial threshold will be calibrated against the three evaluation scenarios rather than treated as a business constant.
- Hard constraints are enforced in code and cannot be overridden by the language model.
- Quantities, dates and shortages are calculated deterministically.
- Recommendations must cite the operational data and tool outputs used to produce them.
- Every action with operational or financial impact requires explicit planner approval.
- The system must degrade safely when a tool or data source is unavailable: it reports which evidence is missing and does not issue a recommendation that depends on it.

## Evaluation

This prototype is evaluated as both an operational decision-support workflow and a reusable presales asset. It does not claim production ROI. Results will be published here only once they have actually been measured.

- **V1, does it work?** Correct identification of impacted orders, deterministic calculation accuracy, time to a decision-ready set of options, correct tool path, recommendation traceability, successful demo runs.
- **V2, is it robust and reusable?** Planner override behaviour, failure recovery, customisation time, reuse by another solution consultant.

Method, golden dataset and metric definitions: [`docs/evaluation.md`](docs/evaluation.md)

## Status

| Stage | Status |
|---|---|
| Synthetic manufacturing dataset and scenario definitions | 🟡 in progress |
| Supplier-delay event → affected purchase orders (script) | 🟡 in progress |
| Golden files: expected results defined per scenario | ⚪ planned |
| SQLite mini-ERP and relational data model | ⚪ planned |
| Deterministic supplier-delay impact engine, tested against golden files | ⚪ planned |
| Tool/API layer for ERP-style queries | ⚪ planned |
| Agentic investigation and recommendation layer | ⚪ planned |
| Human approval and audit trail | ⚪ planned |
| MCP interface where technically justified | ⚪ planned |
| Three-scenario demo | ⚪ planned |
| Presales enablement kit | ⚪ planned |

Full plan: [`ROADMAP.md`](ROADMAP.md)

## Project structure

```
data/       synthetic ERP data (fictional company and suppliers)
scripts/    Python scripts
docs/       architecture, scenarios, decision policy, evaluation
engine/     deterministic impact engine (planned)
eval/       golden files and evaluation runs (planned)
```

## Build journal

This project documents my progression from enterprise operations and process improvement into hands-on techno-functional AI solution building. Major architectural decisions, failed approaches, technical milestones and iterations are documented in [`JOURNAL.md`](JOURNAL.md) and reflected in the commit history.

## Note

All companies, suppliers, products and figures are fictional. This benchmark environment is a simulation; nothing here is a claim about production ERP performance.
