# Evaluation framework

Principle: **define what "good" means before measuring the model.** Every scenario has a golden record written *before* the agent is run.

This prototype does not claim production ROI. All timings are measured inside the prototype environment; they are not a claim about production ERP performance.

## Golden dataset

One file per scenario in `eval/golden/`, written on Day 3, before the engine or the agent exists:

| Field | Example (scenario A) |
|---|---|
| Input event | Supplier A, PO-2047, delivery moved from 8 Oct to 22 Oct |
| Expected affected objects | PO-2047, MO-881, SO-391 |
| Expected calculations | shortage = 280 units; SO-391 shipment at risk = yes |
| Valid options | Supplier B (feasible); expedite Supplier A (not feasible: arrives 15 Oct) |
| Expected tool path | affected POs → production orders → shortage → customer orders → alternate supply → expedite options → recommendation |
| Tools that should NOT be needed | production resequencing |

The run is then compared automatically against this record.

## V1 — Does it work?

| Metric | How it is measured |
|---|---|
| Impact identification precision / recall | Affected POs, MOs and SOs found vs. golden list |
| Deterministic calculation accuracy | Shortages, dates, quantities, costs vs. golden values (target: 100%) |
| Tool path quality | Required tools called / total tools called; number of unnecessary calls; stopped when evidence was sufficient (scenario B) |
| Time to decision-ready insight | Time from event to a cited set of options, agentic run vs. a manual run of the same steps in the prototype |
| Manual lookups replaced | Number of queries/screens in the manual baseline that the agent performs |
| Recommendation traceability | Every figure in the recommendation maps to a tool output |
| Demo execution success rate | End-to-end runs without technical intervention, out of 10 per scenario |

## V2 — Is it robust and reusable?

| Metric | How it is measured |
|---|---|
| Planner override rate and reasons | Scenarios where a feasible recommendation is commercially undesirable, or where two options are numerically close and the planner decides on context the system does not have. The goal is not a 100% approval rate: the interesting output is *where human judgement adds information the system lacks*. |
| Failure recovery | Simulated tool or data failure: does the agent report the missing evidence and withhold the dependent recommendation? |
| Customisation time | Time to adapt the demo to a new scenario or customer context |
| Presales reusability | A person who has never seen the repo runs a scenario, understands the result and explains the business problem in two minutes. Measured: time to first successful demo, help requests, setup failures, narrative accuracy |

## Is AI necessary here?

Reviewed at each milestone: *would deterministic automation solve this step equally well?* The current answer is the table "Where AI is used, and where it is not" in the README. If a step does not need AI, it does not get AI.
