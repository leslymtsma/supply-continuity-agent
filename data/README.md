# Data dictionary

## `purchase_orders.csv`

Purchase orders (POs) sent by a fictional industrial pump manufacturer to its suppliers. Used on Days 1–2, before the full SQLite dataset.

| Column | Meaning |
|---|---|
| `po_id` | Purchase order number |
| `supplier` | Supplier name |
| `item_code` | Internal part number of the component |
| `item_description` | What the component is |
| `quantity`, `unit` | How much was ordered |
| `order_date` | When the order was sent |
| `promised_date` | Delivery date the supplier originally committed to |
| `expected_date` | Latest delivery date known before the new event |
| `status` | `open`, `partially_received` or `received` |
| `production_order` | Manufacturing order (MO) that consumes this component |

## Business rules

- A supplier delay event applies to that supplier's purchase orders that are **not fully received** (`open` or `partially_received`).
- **New expected date** = current `expected_date` + delay announced in the event.
- **Lateness vs. commitment** = new expected date − `promised_date`.
- A `received` PO is never affected: the goods are already here.
- A `partially_received` PO is affected only for the quantity still to come (the remaining quantity is not in this file yet).
