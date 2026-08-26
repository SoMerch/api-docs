# SoMerch API Endpoint Reference

Complete reference for the SoMerch Integrations API used by the official SoMerch Zapier app and custom integrations.

- Base URL: `https://somerch.co`
- OpenAPI 3.1 spec: [`../openapi.json`](../openapi.json) (also published at `https://docs.somerch.co/openapi.json`)
- Interactive reference: `https://docs.somerch.co/openapi.html`
- Machine discovery: `https://somerch.co/api/public/openapi.json`

Authentication guide: [authentication.md](authentication.md)

---

## OAuth 2.0

Connect an integration (such as the SoMerch Zapier app) to a SoMerch organization. Client credentials are provided when the integration is set up.

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/oauth/authorize` | Browser consent screen (authorization code + optional PKCE). |
| POST | `/api/public/v1/oauth2/token` | Exchange a code, or refresh tokens (grants `authorization_code`, `refresh_token`). Client credentials in body or HTTP Basic. |
| POST | `/api/public/v1/oauth2/revoke` | Revoke an access or refresh token (RFC 7009). |
| GET | `/api/public/v1/me` | Connection identity and label; called by Zapier to label and verify a connection. |

Scopes: `orders:read`, `shipments:read`, `quotes:read`, `quotes:write`, `catalog:read`, `plans:write`, `webhooks:write`.

---

## Zapier triggers

REST-hook triggers subscribe through `/zapier/subscribe` and are delivered by SoMerch as webhooks. Polling endpoints act as fallback and initial-import source.

| Method | Endpoint | Scope | Purpose |
|---|---|---|---|
| GET | `/api/public/v1/zapier/events` | - | Event catalogue and the scope each event requires. |
| POST | `/api/public/v1/zapier/subscribe` | `webhooks:write` + per-event scope | Register a hook URL for one event type. Returns `{ id }`. |
| DELETE | `/api/public/v1/zapier/unsubscribe/{id}` | `webhooks:write` | Remove a hook subscription (404 tolerated). |
| GET | `/api/public/v1/zapier/triggers/orders` | `orders:read` | Poll orders, newest first. Query: `limit` (default 25, max 100), `since`. |
| GET | `/api/public/v1/zapier/triggers/shipments` | `shipments:read` | Poll shipments, newest first. Query: `limit`, `since`. |
| GET | `/api/public/v1/zapier/triggers/quote-requests` | `quotes:read` | Poll quote requests, newest first. Query: `limit`, `since`. |

Event types: `order.created`, `order.status_changed`, `shipment.created`, `shipment.updated`, `quote.submitted`.

---

## Zapier choices (dropdowns)

Dynamic dropdown sources for Zapier input fields.

| Method | Endpoint | Scope | Purpose |
|---|---|---|---|
| GET | `/api/public/v1/zapier/choices/categories` | `catalog:read` | Product categories (id + label). |
| GET | `/api/public/v1/zapier/choices/order-statuses` | `orders:read` | Order statuses (id + label). |
| GET | `/api/public/v1/zapier/choices/organizations` | - | Organization(s) the token can act on. |

---

## Actions

Central action dispatcher used by Zapier searches and creates. Full input/output schemas: [actions.md](actions.md).

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/public/v1/actions` | List all registered action keys. |
| POST | `/api/public/v1/actions/{actionKey}` | Invoke an action. Body `{ "input": ... }`. Optional `Idempotency-Key` header. |

Actions: `system.ping`, `orders.list`, `orders.get`, `catalog.search_products`, `catalog.get_product`, `quotes.submit`, `plans.create`.

---

## Resources

| Method | Endpoint | Scope | Purpose |
|---|---|---|---|
| GET | `/api/public/v1/providers` | - | Integration provider catalog (optional `include_planned=true`). |
| GET | `/api/public/v1/connections` | - | List integration connections (sanitized). |
| POST | `/api/public/v1/connections` | - | Create a connection stub for a provider slug. |
| GET | `/api/public/v1/connections/{id}` | - | Fetch a single connection. |
| DELETE | `/api/public/v1/connections/{id}` | - | Disconnect a connection. |
| GET | `/api/public/v1/events` | - | Filterable event log (`event_type`, `resource_type`, `resource_id`, `correlation_id`, `from`, `to`, `limit`, `cursor`). |
| GET | `/api/public/v1/webhooks` | - | List outgoing webhook subscriptions. |
| POST | `/api/public/v1/webhooks` | - | Create a subscription; returns the signing `secret` once. |
| PATCH | `/api/public/v1/webhooks/{id}` | - | Update url / event_types / description / is_active. |
| DELETE | `/api/public/v1/webhooks/{id}` | - | Delete a subscription. |
| GET | `/api/public/v1/jobs` | - | List background jobs (`status`, `job_type`, `connection_id`, `limit`). |
| GET | `/api/public/v1/audit-logs` | - | Sanitized audit trail (`action`, `limit`). |

---

## Outgoing webhook delivery

When an event is delivered to a subscribed hook URL, SoMerch sends an HTTPS POST with these headers:

| Header | Description |
|---|---|
| `X-SoMerch-Event` | Event type (for example `order.status_changed`). |
| `X-SoMerch-Delivery` | Delivery id. |
| `X-SoMerch-Timestamp` | Unix seconds. |
| `X-SoMerch-Signature` | `t=<ts>,v1=<hmac-sha256-hex>` |

Verify the signature by computing HMAC-SHA256 over `<ts>.<raw-body>` with the `whsec_...` secret using a timing-safe comparison.

Retries: exponential backoff, maximum 8 attempts. Hooks that keep failing are deactivated.

Zapier hook payloads are flattened to top-level fields so Zapier can bind fields directly; the envelope added for non-Zapier consumers (`id`, `type`, `event_created_at`, `resource`, `correlation_id`, `data`) is documented in [webhooks.md](webhooks.md).

---

## Incoming webhooks

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/api/public/webhooks/{providerSlug}/{connectionId}` | Receive provider webhooks (signature verified); enqueues a job. |
| POST | `/api/public/cron/dispatch-webhooks` | Internal, secret-gated cron entry point. Not for external use. |

---

## Discovery and agent endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/public/health/env` | Environment health probe (no secrets). |
| GET | `/api/public/openapi.json` | OpenAPI 3.1 document. |
| POST | `/mcp` | Model Context Protocol server (Streamable HTTP) used by the ChatGPT plugin. |
| GET | `/.well-known/mcp/server-card.json` | MCP server card (SEP-1649). |
| GET | `/.well-known/agent-skills/index.json` | Agent Skills discovery index. |
| GET | `/.well-known/openid-configuration` | OpenID Connect discovery. |
| GET | `/.well-known/oauth-authorization-server` | OAuth 2.0 authorization server metadata (RFC 8414). |
| GET | `/.well-known/oauth-protected-resource` | OAuth 2.0 protected resource metadata (RFC 9728). |
| GET | `/.well-known/api-catalog` | RFC 9727 API catalog. |
| GET | `/.well-known/openai-apps-challenge` | OpenAI apps domain verification challenge. |

---

## Error model

Successful responses are envelope-shaped: `{ "ok": true, "data": ... }`. Failures return `{ "ok": false, "error": { "code", "message", "retryable" } }` with an appropriate HTTP status. OAuth endpoints follow RFC 6749/7009 error payloads (`error`, `error_description`).