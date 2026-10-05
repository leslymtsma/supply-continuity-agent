# Decision policy (draft)

The agent recommends against these explicit rules. It does not invent its own. All thresholds and weights are fictional and will be calibrated against the evaluation scenarios.

The policy has four layers, each with a different owner.

| Layer | Owner | Can the language model override it? |
|---|---|---|
| 1. Hard constraints | Deterministic code | **No, never** |
| 2. Scored preferences | Deterministic code (baseline ranking) | It may *challenge* the ranking, with reasons, and escalate |
| 3. Contextual considerations | AI surfaces them | n/a: this is the AI's contribution |
| 4. Final exception decision | Planner | n/a: human authority |

## 1. Hard constraints

An option that fails any hard constraint is marked **ineligible** by code, before the model sees the list. The agent may mention an ineligible option only to explain why it was excluded.

| Constraint | Rule |
|---|---|
| Timing | Supply must arrive before the required date of the production order |
| Quantity | The option must cover the shortage (alone or in a stated combination) |
| Blocked supplier | Suppliers flagged as blocked are never eligible |
| Budget ceiling | Extra cost above €2,000 is not eligible for recommendation; the agent lists it as "requires a budget exception" for the planner |

Example of what the agent may **not** say: *"This supplier arrives too late, but given the customer's strategic importance I recommend it anyway."*

## 2. Scored preferences (deterministic baseline)

Eligible options are ranked by code, in this order of priority:

1. **Protect customer commitments**, with a higher weight for orders flagged as strategic
2. **Minimise extra cost**
3. **Minimise disruption to the production plan** (avoid resequencing when another option exists)
4. **Prefer reversible actions** over irreversible ones

Weights are configuration, not constants, and will be tuned with the scenarios.

## 3. Contextual considerations (AI)

Information the score does not capture, which the agent surfaces when present:

- a free-text supplier message mentioning a possible partial shipment or a new risk
- a quality note or ongoing dispute with a supplier
- a customer note that changes the importance of a date
- two eligible options with near-equal scores

When this context is relevant, the agent may challenge the deterministic ranking. It cannot override hard constraints or execute the alternative. It must explain the discrepancy and escalate the decision to the planner.

## 4. Final exception decision (planner)

The planner approves, rejects or changes the recommendation. Any decision outside the ranking is the planner's, and the reason is recorded in the audit log.

## What every recommendation must include

- The eligible options with their deterministic figures, and the ineligible ones with the constraint they failed
- The recommended option and the priority that justifies it
- Any challenge to the ranking, with its reason
- The data and tool outputs used (IDs)
- The open questions only the planner can answer (e.g. reduce the original PO or keep it?)
