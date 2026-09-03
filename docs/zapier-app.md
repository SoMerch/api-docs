# Zapier App

SoMerch offers an official **Zapier app** for no-code automation. The app is approved and listed in the [Zapier Marketplace](https://zapier.com/apps/somerch/integrations). Connect your SoMerch account with OAuth 2.0 and build zaps that react to merch events and take actions across 8000+ apps.

The app is built entirely on the SoMerch Integrations API documented in this repository:

- [OpenAPI 3.1 specification](../openapi.json) (also published at `https://docs.somerch.co/openapi.json`)
- [Endpoint reference](endpoint-reference.md)
- [Authentication guide](authentication.md)
- [Actions reference](actions.md)
- [Webhooks](webhooks.md)

## Connection

The SoMerch Zapier app uses OAuth 2.0 against SoMerch's own authorization server. When you connect the app in Zapier, you authorize scopes for your organization. Access and refresh tokens are handled automatically and refresh tokens rotate on every use.

## Triggers

| Trigger | Event | Delivery |
|---|---|---|
| New Order | `order.created` | REST hook with polling fallback |
| Order Status Changed | `order.status_changed` | REST hook with polling fallback |
| New Shipment | `shipment.created` | REST hook with polling fallback |
| Shipment Updated | `shipment.updated` | REST hook with polling fallback |
| New Quote Request | `quote.submitted` | REST hook with polling fallback |

Hooks are registered via `POST /api/public/v1/zapier/subscribe` and removed via `DELETE /api/public/v1/zapier/unsubscribe/:id`. Polling endpoints (`/api/public/v1/zapier/triggers/orders`, `/shipments`, `/quote-requests`) act as automatic fallback and return newest-first with a configurable limit.

## Searches

| Search | Backing action |
|---|---|
| Find Products | `catalog.search_products` (category dropdown + max price filter) |
| Find Product | `catalog.get_product` (by slug or dynamic product list) |
| Find Order | `orders.get` |

## Creates

| Create | Backing action | Notes |
|---|---|---|
| Submit Quote Request | `quotes.submit` | Requires consent to terms; idempotent per zap (`zap-<zapId>-<email>-<plan>-<option>`); rejects personal emails |
| Create Merch Plan | `plans.create` | Returns tiered options, plan reference, and widget link |

## Event catalogue

Event types available to subscribers of the Zapier app: `order.created`, `order.status_changed`, `shipment.created`, `shipment.updated`, `quote.submitted`. The full event stream also includes `store_order.created` and `redeem_claim.created`.

## Reliability

- Retries with exponential backoff (30s to 24h, max 8 attempts) with auto-deactivation after repeated failures.
- Webhook URLs are SSRF-guarded before every delivery.
- Signatures (`X-SoMerch-Signature: t=<ts>,v1=<hex>`) are included on every delivery for consumers that verify them.
- Write actions support idempotency keys (24h TTL) so retries never double-fire.

## Getting started

1. Open the [SoMerch app in the Zapier Marketplace](https://zapier.com/apps/somerch/integrations).
2. Connect your SoMerch account (OAuth consent).
3. Choose a trigger, search, or create from the lists above.
4. For creates such as "Submit Quote Request", make sure `consent_terms` is set to `true` and map the plan reference or plan id.

## Related resources

- REST API: [public-api.md](public-api.md)
- Webhooks: [webhooks.md](webhooks.md)
- Public docs: [somerch.co/docs/integrations](https://somerch.co/docs/integrations)