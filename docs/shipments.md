
---

# `docs/shipments.md`

```md
# Shipments

This document describes planned shipment-related API concepts for the SoMerch platform.

Shipments represent parcels or delivery groups sent to employees, offices, events, or partners.

---

## Shipment statuses

| Status | Description |
|---|---|
| `pending` | Shipment has been created but not processed. |
| `ready_to_pack` | Items are ready for warehouse packing. |
| `packed` | Shipment has been packed. |
| `label_created` | Shipping label has been generated. |
| `in_transit` | Shipment is with the carrier. |
| `delivered` | Shipment has been delivered. |
| `returned` | Shipment has been returned. |
| `failed_delivery` | Delivery failed. |
| `on_hold` | Shipment is paused due to missing data or operational issue. |

---

## Example shipment object

```json
{
  "id": "shp_12345",
  "order_id": "ord_98765",
  "status": "in_transit",
  "carrier": "DHL",
  "tracking_number": "JD014600006789000000",
  "tracking_url": "https://example-carrier.com/track/JD014600006789000000",
  "destination_country": "Germany",
  "created_at": "2026-06-05T10:00:00Z"
}
