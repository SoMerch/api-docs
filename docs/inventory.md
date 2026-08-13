# Inventory

This document describes inventory-related API concepts for the SoMerch platform.

Inventory visibility is important for teams that store merch, reorder frequently, or ship items to multiple locations.

> **Live API:** See the [OpenAPI spec](https://somerch.co/api/public/openapi.json) and [integration docs](https://somerch.co/docs/integrations) for the current REST API endpoints. The concepts below describe the planned domain model.

---

## Inventory concepts

| Concept | Description |
|---|---|
| SKU | A stock keeping unit for a specific product variant. |
| Available stock | Quantity currently available for new orders. |
| Reserved stock | Quantity reserved for approved or active orders. |
| Incoming stock | Quantity expected from production or supplier intake. |
| Batch | A group of items received, produced, or stored together. |
| Kit stock | Availability of grouped products used in packs or kits. |

---

## Example inventory object

```json
{
  "sku": "hoodie-navy-m",
  "product_id": "prod_hoodie_001",
  "variant_id": "var_navy_m",
  "warehouse": "EU-HUB-1",
  "available_quantity": 120,
  "reserved_quantity": 35,
  "incoming_quantity": 200,
  "low_stock_threshold": 50,
  "updated_at": "2026-06-05T10:00:00Z"
}
```