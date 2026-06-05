
---

# `docs/webhooks.md`

```md
# Webhooks

This document describes planned webhook concepts for the SoMerch platform.

Webhooks allow external systems to receive event notifications when important changes happen in SoMerch.

---

## Planned event categories

| Category | Example events |
|---|---|
| Orders | `order.created`, `order.updated`, `order.approved`, `order.cancelled` |
| Inventory | `inventory.updated`, `inventory.low_stock`, `inventory.received` |
| Shipments | `shipment.created`, `shipment.updated`, `shipment.delivered`, `shipment.returned` |
| Approvals | `approval.created`, `approval.approved`, `approval.rejected` |
| Fulfillment | `fulfillment.started`, `fulfillment.completed`, `fulfillment.issue_detected` |

---

## Example webhook payload

```json
{
  "event": "order.updated",
  "created_at": "2026-06-05T10:30:00Z",
  "data": {
    "order_id": "ord_98765",
    "previous_status": "approved",
    "status": "in_production"
  }
}
