# Orders

This document describes planned order-related API concepts for the SoMerch platform.

The order lifecycle connects quote requests, production, approval, inventory, fulfillment, and shipment tracking.

---

## Order lifecycle

A typical SoMerch order may move through the following statuses:

| Status | Description |
|---|---|
| `draft` | The order has been created but is not ready for review. |
| `quote_requested` | A quote has been requested. |
| `quoted` | Pricing and production details are available. |
| `approval_required` | The order requires internal approval before proceeding. |
| `approved` | The order has been approved. |
| `in_production` | Production or branding has started. |
| `quality_check` | The order is being checked before fulfillment. |
| `ready_for_fulfillment` | The order is ready to be picked, packed, or shipped. |
| `partially_fulfilled` | Some items or shipments have been fulfilled. |
| `fulfilled` | The order has been fully fulfilled. |
| `cancelled` | The order has been cancelled. |
| `on_hold` | The order is paused due to missing information, stock, approval, or payment. |

---

## Example order object

```json
{
  "id": "ord_98765",
  "status": "in_production",
  "customer_reference": "Q4-onboarding-pack",
  "budget_tag": "HR-Onboarding-2026",
  "currency": "EUR",
  "destination_type": "multi_address",
  "created_at": "2026-06-05T10:00:00Z",
  "items": [
    {
      "product_id": "prod_hoodie_001",
      "variant_id": "var_navy_m",
      "quantity": 50,
      "decoration_method": "screen_print"
    }
  ]
}
