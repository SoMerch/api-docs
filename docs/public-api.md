# Public REST API

SoMerch provides a public REST API for programmatic access to platform data.

## Base URL

```
https://somerch.co/api/public/v1/
```

## OpenAPI specification

The full OpenAPI specification is available at:

```
https://somerch.co/api/public/openapi.json
```

## Authentication

The REST API uses OAuth 2.0 for authentication. The authorization flow supports multiple providers and per-organization scoping.

### OAuth endpoints

| Endpoint | Description |
|---|---|
| `/api/public/v1/oauth/{provider}/authorize` | Initiate OAuth authorization |
| `/api/public/v1/oauth/{provider}/callback` | OAuth callback handler |

## API resources

| Resource | Description |
|---|---|
| **Actions** | Pre-defined integration actions |
| **Audit logs** | Integration activity records |
| **Connections** | Manage OAuth connections |
| **Events** | Event stream for integration triggers |
| **Jobs** | Asynchronous job management |
| **Providers** | Available OAuth providers |
| **Webhooks** | Webhook subscription management |

## Webhooks

SoMerch supports outgoing webhooks to notify external systems of platform events. Webhooks are configured per connection and include signature verification for security.

### Webhook endpoints

| Endpoint | Description |
|---|---|
| `/api/public/webhooks/{provider}/{connection}` | Receive webhook events |
| `/api/public/v1/webhooks` | Manage webhook subscriptions |
| `/api/public/v1/webhooks/{id}` | Manage individual webhook |

### Event types

Webhook events are organized by category:
- Order events (created, updated, approved, cancelled)
- Inventory events (updated, low stock, received)
- Shipment events (created, updated, delivered, returned)
- Approval events (created, approved, rejected)
- Fulfillment events (started, completed, issue detected)

## Signature verification

Every webhook payload includes a signature header. Verify the signature using the shared secret to ensure the payload originated from SoMerch and has not been tampered with.

## Rate limits

The REST API enforces rate limits to ensure fair usage. Limit details are included in response headers.

## Idempotency

Write operations support idempotency keys to safely retry requests without creating duplicate resources.

## Integration docs

For the full API reference, interactive documentation, and integration guides, visit:

- Integration docs: [somerch.co/docs/integrations](https://somerch.co/docs/integrations)
- OpenAPI spec: [somerch.co/api/public/openapi.json](https://somerch.co/api/public/openapi.json)