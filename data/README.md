# Data dictionary

## `purchase_orders.csv`

Purchase orders (POs) sent by a fictional industrial pump manufacturer to its suppliers.

| Column | Meaning |
|---|---|
| `po_id` | Purchase order number |
| `supplier` | Supplier name |
| `item_code` | Internal part number |
| `item_description` | What the part is |
| `quantity`, `unit` | How much was ordered |
| `order_date` | When we sent the order |
| `promised_date` | Delivery date the supplier committed to |
| `expected_date` | Latest delivery date the supplier announced (ETA) |
| `status` | `open`, `partially_received` or `received` |
| `work_order` | Production order (WO) that needs this part |

## Business rules

- A PO is **delayed** when `expected_date` is after `promised_date` **and** it is not fully `received`.
- **Days of delay** = `expected_date` − `promised_date`.
- A received PO is never delayed, whatever the dates say: the goods are here.
