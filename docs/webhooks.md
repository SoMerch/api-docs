# Webhooks

This document describes webhook concepts for the SoMerch platform.

Webhooks allow external systems to receive event notifications when important changes happen in SoMerch. They are live and also power the SoMerch Zapier triggers.

> **Live API:** SoMerch's REST API includes webhook subscription management and signature verification. See the [OpenAPI spec](https://somerch.co/api/public/openapi.json) and [integration docs](https://somerch.co/docs/integrations).

---

## Live event types

| Event | Triggered when |
|---|---|
| `order.created` | A new order is inserted |
| `order.status_changed` | An order's status changes |
| `shipment.created` | A new shipment is created |
| `shipment.updated` | A shipment's name, tracking, carrier, or service changes |
| `quote.submitted` | A quote request is submitted (from the website, ChatGPT plugin, or API) |
| `store_order.created` | A store order is created |
| `redeem_claim.created` | A redeem claim is created |

---

## Example webhook payload

```json
{
  "id": "evt_01JEHEJ3VQF9GTYS3BC8P3JXSQ",
  "type": "order.status_changed",
  "event_created_at": "2026-08-27T10:30:00Z",
  "resource": "order",
  "correlation_id": "e5c0d6b8e323c1b99a30d6f4f9c2f8a6c12e5b1c",
  "data": {
    "id": "ord_12345",
    "status_id": "in_production",
    "previous_status_id": "approved"
  }
}
```

---

## Delivery

Deliveries are sent as JSON `POST` requests to the subscribed hook URL. When a hook was created through the SoMerch Zapier app, the payload is flattened to top-level fields so Zapier can bind fields directly.

### Delivery headers

| Header | Description |
|---|---|
| `X-SoMerch-Event` | Event type |
| `X-SoMerch-Delivery` | Delivery attempt id |
| `X-SoMerch-Timestamp` | Delivery timestamp |
| `X-SoMerch-Signature` | `t=<ts>,v1=<hmac-sha256-hex>` signature |

### Signature verification

Compute an HMAC-SHA256 of `<timestamp>.<raw-body>` using the webhook secret from the `whsec_...` value returned when the webhook was created, then compare it to the `v1` value. Always use a constant-time comparison.

---

## Retries and failure handling

- Retry with exponential backoff from 30 seconds up to 24 hours (maximum 8 attempts).
- A webhook is automatically deactivated after repeated failures.
- Deliveries can be replayed after the destination becomes available.
- The webhook URL is validated before each delivery to prevent SSRF; unsafe URLs cancel and deactivate the hook.

---

## Subscription management

| Endpoint | Description |
|---|---|
| `POST /api/public/v1/zapier/subscribe` | Register a hook URL for an event (used by the Zapier app) |
| `DELETE /api/public/v1/zapier/unsubscribe/:id` | Remove a hook subscription |
| `GET /api/public/v1/zapier/events` | List the event catalogue and required scopes |

Zapier triggers use REST hooks with an automatic polling fallback (`GET /api/public/v1/zapier/triggers/...`), so zaps keep working even when hook delivery is unavailable.

See [Zapier app](zapier-app.md) for trigger details.