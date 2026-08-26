# Public REST API

SoMerch provides a public REST API for programmatic access to platform data. It powers custom integrations as well as the official SoMerch Zapier app.

## Base URL

```
https://somerch.co/api/public/v1/
```

## OpenAPI specification

The full OpenAPI 3.1 specification is available at:

- `../openapi.json` (this repository; also published at `https://docs.somerch.co/openapi.json`)
- Interactive reference: `https://docs.somerch.co/openapi.html`
- Machine-readable copy on the live host: `https://somerch.co/api/public/openapi.json`

## Authentication

The REST API uses OAuth 2.0 for authentication with per-organization scoping. Both authorization code and refresh token flows are supported, with optional PKCE and rotating refresh tokens.

### OAuth endpoints

| Endpoint | Description |
|---|---|
| `/oauth/authorize` | OAuth 2.0 authorization (consent) endpoint |
| `/api/public/v1/oauth2/token` | Exchange authorization code or refresh token |
| `/api/public/v1/oauth2/revoke` | Revoke an access or refresh token |
| `/api/public/v1/me` | Current connection identity and label |

A documented scope set is available (for example `orders:read`, `shipments:read`, `quotes:write`, `catalog:read`, `plans:write`, `webhooks:write`).

See [authentication.md](authentication.md) for the full flow with examples.

## Actions

Actions are pre-defined integration operations invoked through a single dispatcher:

```
POST /api/public/v1/actions/:actionKey
```

Live action keys:

| Action | Description | Required scope |
|---|---|---|
| `catalog.search_products` | Search products with quantity-aware pricing | `catalog:read` |
| `catalog.get_product` | Get a single product by id or slug | `catalog:read` |
| `orders.get` | Get an order by id | `orders:read` |
| `orders.list` | List orders | `orders:read` |
| `quotes.submit` | Submit a quote request (shared core with the ChatGPT MCP tool) | `quotes:write` |
| `plans.create` | Create a merch plan (same engine as the MCP planner) | `plans:write` |

Write actions accept an `Idempotency-Key` header (24-hour TTL) so retries never create duplicates. A small positive response is returned on success; error responses use a structured `{ ok: false, error: { code, message } }` envelope.

See [actions.md](actions.md) for the full input/output schemas of every action and [endpoint-reference.md](endpoint-reference.md) for the complete endpoint catalog.

## API resources

| Resource | Description |
|---|---|
| **Actions** | Pre-defined integration actions (see above) |
| **Audit logs** | Integration activity records |
| **Connections** | Manage OAuth connections |
| **Events** | Event stream for integration triggers |
| **Jobs** | Asynchronous job management |
| **Providers** | Available OAuth providers |
| **Webhooks** | Outgoing webhook subscription management |
| **Zapier** | Polling and choice endpoints used by the Zapier app |

## Webhooks

SoMerch supports outgoing webhooks to notify external systems of platform events. Webhooks are org-scoped and include signature verification.

### Webhook endpoints

| Endpoint | Description |
|---|---|
| `/api/public/v1/webhooks` | Manage webhook subscriptions (create, list) |
| `/api/public/v1/webhooks/{id}` | Manage an individual webhook |
| `/api/public/v1/zapier/subscribe` | Subscribe a Zapier hook URL to an event |
| `/api/public/v1/zapier/unsubscribe/{id}` | Unsubscribe a Zapier hook |
| `/api/public/v1/zapier/triggers/orders` | Poll orders (newest first) for polling triggers |
| `/api/public/v1/zapier/triggers/shipments` | Poll shipments (newest first) for polling triggers |
| `/api/public/v1/zapier/triggers/quote-requests` | Poll quote requests (newest first) for polling triggers |

### Event types

Live webhook event types:

- `order.created`
- `order.status_changed`
- `shipment.created`
- `shipment.updated`
- `quote.submitted`

## Signature verification

Every webhook payload includes an `X-SoMerch-Signature` header in the form `t=<timestamp>,v1=<hmac-sha256-hex>` computed over `<timestamp>.<body>` with the webhook secret (`whsec_...`). Verify the signature to ensure the payload originated from SoMerch and has not been tampered with.

Delivery attempts retry with exponential backoff (from 30 seconds up to 24 hours, maximum 8 attempts). A webhook is automatically deactivated after repeated failures.

## Zapier app

SoMerch provides an official Zapier app built on this API. It uses OAuth 2.0 connections, REST-hook triggers with automatic polling fallback, and the action dispatcher for searches and creates. See [Zapier app](zapier-app.md) for details.

## Rate limits

The REST API enforces rate limits to ensure fair usage. Limit details are included in response headers.

## Idempotency

Write operations support idempotency keys to safely retry requests without creating duplicate resources.

## Integration docs

For the full API reference, interactive documentation, and integration guides, visit:

- Integration docs: [somerch.co/docs/integrations](https://somerch.co/docs/integrations)
- OpenAPI spec: [somerch.co/api/public/openapi.json](https://somerch.co/api/public/openapi.json)