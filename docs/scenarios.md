# Demo scenarios

Three scenarios built on the same chain, designed so that the agent takes a **different investigation path** in each one. This is what separates an agent from a fixed workflow.

The chain:

```
Supplier → Component → Purchase order (PO) → Production order (MO) → Product → Customer order (SO)
```

All figures are draft values, to be finalized when the dataset is built (Day 4).

---

## Scenario A — Shortage, alternate supply available

**Event (received 1 Oct):** Supplier A moves delivery of PO-2047 from **8 Oct** to **22 Oct**.

| Object | Data |
|---|---|
| PO-2047 | Supplier A, component C-104, 500 units, expected 8 Oct → now 22 Oct |
| MO-881 | Product P-22, needs 400 × C-104, production starts 11 Oct |
| SO-391 | Customer X, P-22, promised shipment 14 Oct |
| Inventory C-104 | 120 units available |
| Supplier B | 300 units available, +12% unit cost, 5-day lead time (arrives 6 Oct) |
| Expedite with Supplier A | Partial air shipment possible, arrives 15 Oct, too late for MO-881 |

**What the agent should discover:**
- Shortage = 400 − 120 = **280 units** (deterministic)
- Supplier B arrives 6 Oct, before production on 11 Oct → **feasible**
- Expediting with Supplier A arrives 15 Oct → **not feasible**

**Expected recommendation:**
> Source 280 units of C-104 from Supplier B (+12%, extra cost €X) to preserve the SO-391 shipment date. Open question for the planner: reduce PO-2047 by 280 units or keep it for future demand? **Awaiting planner approval.**

---

## Scenario B — Inventory covers the need

**Event:** a supplier delays a component, but available inventory covers the requirement of the next production order.

**Expected path:** the agent checks inventory, finds no shortage, and **stops**.

**Expected recommendation:**
> No action needed. Inventory covers MO-xxx. Monitor the new delivery date for the following production order.

---

## Scenario C — No alternative in time

**Event:** a supplier delays a component, inventory does not cover the need, and no alternate supplier can deliver before production starts.

**Expected path:** inventory → alternate suppliers (none in time) → expedite (not enough) → **production resequencing** or **customer date change**.

**Expected recommendation:**
> Two options for the planner: (1) swap MO-xxx with MO-yyy, which does not use this component, or (2) propose a new shipment date to Customer Y for SO-zzz. Both require human decision.

---

## What we test

For each scenario: did the agent call the right tools, in a sensible order, stop when it should, and leave every operational change to the planner?

---

## Override cases (V2)

Scenarios designed so that the right answer depends on context the agent does not have. The goal is not a high approval rate: it is to see whether the agent surfaces the question instead of deciding.

- **Feasible but commercially undesirable:** the cheapest option uses a supplier the planner knows is in a quality dispute. The planner rejects.
- **Near-equal options:** two options with similar cost and delay. The planner chooses based on customer relationship context.
- **Missing data:** the alternate supplier's lead time is unknown. The agent must report the gap, not assume a value.
