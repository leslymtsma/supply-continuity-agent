# Architecture

*Target design. Updated as components are built.*

## Layers

```
Supplier delay event (structured, or free-text email)
        │
        ▼
Agent layer (LLM)        decides what to investigate next, interprets options, explains, recommends
        │ calls only allowed tools
        ▼
Tool layer               plain Python functions with typed inputs/outputs
        │                exposed through two adapters: direct API tool use, and MCP
        ▼
Deterministic engine     relationships, shortages, dates, costs, feasibility of options
        │
        ▼
Data (SQLite mini-ERP)   read-only for the agent
        +
Proposals & audit log    the only place the agent can write
        │
        ▼
Planner approval         the only path to an operational change
```

## Who does what

| Deterministic (code and business rules) | AI / agentic | Human (planner) |
|---|---|---|
| Delivery dates and quantities | Understand an unstructured supplier message | Approve a supplier change |
| Available inventory | Decide which additional information is needed | Accept expedited shipping |
| PO → component → production order → customer order links | Orchestrate the allowed tools | Change a customer promise date |
| Shortage calculation | Summarize the impact | Approve an additional cost |
| Option feasibility, cost and delay figures | Interpret structured options and explain their trade-offs | |
| | Recommend an option against the decision policy | |

The deterministic layer produces options such as:

| Option | Extra cost | Customer date | Constraint |
|---|---|---|---|
| A — Alternate supplier | +€1,400 | preserved | — |
| B — Wait | €0 | +8 days | — |
| C — Resequence production | +€400 | preserved | rescheduling required |

The AI layer contextualises these options, identifies qualitative trade-offs, explains them, and recommends one against the explicit priorities in [`decision-policy.md`](decision-policy.md). It does not perform an opaque optimisation.

## AI-necessity review

| Function | AI required? | Why |
|---|---|---|
| Calculate shortage | No | Deterministic arithmetic |
| Resolve PO → MO → SO relationships | No | Structured relational query |
| Calculate alternative supplier cost and arrival date | No | Deterministic |
| Parse a free-text supplier email | Useful | Unstructured language |
| Decide what to investigate next | Useful | Path depends on evidence |
| Explain trade-offs to a planner | Useful | Contextual synthesis |
| Execute a supplier switch | No autonomous AI | Controlled transaction + human approval |

## Governance

- **Tool permissions:** operational systems are read-only to the agent. The agent may write only to its proposal store and audit log. It cannot directly modify purchase orders, inventory, production plans or customer commitments.
- **Hard constraints in code:** options that fail a hard constraint (see [`decision-policy.md`](decision-policy.md)) are marked ineligible before the model sees them.
- **Tool-call budget:** configurable, to prevent uncontrolled loops. The initial threshold will be calibrated against the three evaluation scenarios rather than treated as a business constant.
- **Audit:** every tool call, input, output and proposal is logged with a timestamp.
- **Safe degradation:** if a tool fails or data is missing, the agent reports the gap and what it could not verify, instead of guessing.

## Why MCP, and when

The supply-chain logic does not require MCP. The build sequence is:

1. Build the business tools independently, as plain Python functions over the deterministic engine.
2. Expose them through a conventional tool/API layer (direct tool use with the model API).
3. Once the interfaces are stable, expose the relevant capabilities through MCP, at the interoperability layer, so the tools are not tightly coupled to one agent implementation.

MCP-compatible clients can then potentially consume the same exposed tools, subject to authentication, permissions and client capabilities. The tool logic is written once.

MCP is required by the learning objective of this project and useful for the intended reuse model, but it is not required to solve the underlying supply-chain problem.
