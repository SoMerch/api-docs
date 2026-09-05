# SoMerch API Docs

Developer-oriented documentation for the SoMerch platform.

SoMerch is a software-powered corporate merch platform and service partner for distributed companies across Europe. The platform connects product curation, order management, inventory visibility, warehousing, approvals, fulfillment, and EU-wide shipping.

This repository documents SoMerch's API concepts, as well as the live public MCP server and REST API.

---

## Status

SoMerch offers a public **MCP server** (including a **ChatGPT plugin**), a **public REST API**, and an official **Zapier app**, all live and available for integration.

| Resource | Endpoint | Status |
|---|---|---|
| Public MCP server | `somerch.co/mcp` | **Live** |
| ChatGPT plugin (OpenAI GPT connector) | `somerch.co/mcp`, listed in the [ChatGPT plugin directory](https://chatgpt.com/plugins/plugin_asdk_app_6a84141dbb1c8191acdb877b8b846b8a) | **Live** |
| MCP server card | `somerch.co/.well-known/mcp/server-card.json` | **Live** |
| OpenAI apps domain challenge | `somerch.co/.well-known/openai-apps-challenge` | **Live** |
| Agent skills discovery | `somerch.co/.well-known/agent-skills/` | **Live** |
| REST API OpenAPI spec | `[openapi.json](openapi.json)` (also `docs.somerch.co/openapi.json`) | **Live** |
| REST API base | `somerch.co/api/public/v1/` | **Live** |
| Zapier app | [Zapier Marketplace listing](https://zapier.com/apps/somerch/integrations) | **Live, approved** |
| Integration docs | `somerch.co/docs/integrations` | **Live** |

### Integration status

SoMerch is building an integration ecosystem around the tools teams already use. Current status of each integration:

| Integration | Status |
|---|---|
| Zapier app | **Live** - approved and listed on the [Zapier Marketplace](https://zapier.com/apps/somerch/integrations) |
| Public MCP server | **Live** |
| Public REST API | **Live** |
| ChatGPT connector | **Live** - approved and listed in the [ChatGPT plugin directory](https://chatgpt.com/plugins/plugin_asdk_app_6a84141dbb1c8191acdb877b8b846b8a), backed by the public MCP server |
| Microsoft Copilot | In review |
| Canva app | In review |
| monday.com app | In review |
| Google Sheets app (Google Workspace Marketplace) | In review |
| Salesforce | In development |
| Deel | In development |
| HubSpot | In development |
| HiBob | In development |

For the latest status, see [somerch.co/docs/integrations](https://somerch.co/docs/integrations).

The documentation in this repository describes platform concepts, live API resources, webhook events, and example data structures. The complete endpoint reference for the integrations API (used by the Zapier app and custom clients) is:

- [OpenAPI 3.1 specification](openapi.json)
- [Endpoint reference](docs/endpoint-reference.md)
- [Actions reference](docs/actions.md)
- [Authentication guide](docs/authentication.md)
- [Webhooks](docs/webhooks.md)

For integration requests, contact the SoMerch team through [somerch.co](https://somerch.co).

---

## Live resources

### MCP server and ChatGPT plugin

SoMerch exposes a public MCP (Model Context Protocol) server for AI agent integration. Clients can connect to `somerch.co/mcp` to browse the catalog, search products, plan merch projects, and submit quote requests. The server is also the connector behind SoMerch's **ChatGPT plugin**: ChatGPT users can add SoMerch as a connector and plan merch, compare products, and request quotes directly in a chat.

See: [`docs/public-mcp.md`](docs/public-mcp.md) and [`docs/chatgpt-plugin.md`](docs/chatgpt-plugin.md)

### REST API

SoMerch provides a public REST API at `/api/public/v1/` with OAuth 2.0 authentication, webhooks, events, actions, and connections management. It powers custom integrations as well as the official **Zapier app**.

See: [`docs/public-api.md`](docs/public-api.md)

### Zapier app

SoMerch offers an official **Zapier app**, approved and listed on the [Zapier Marketplace](https://zapier.com/apps/somerch/integrations), for no-code automation. Connect your SoMerch account with OAuth 2.0 and use triggers (new orders, order status changes, new shipments, shipment updates, new quote requests), searches (products, orders), and actions (submit quote requests, create merch plans) to connect merch operations to 8000+ other apps.

See: [`docs/zapier-app.md`](docs/zapier-app.md)

---

## What this API is designed for

The SoMerch API is intended to help companies connect merch operations with their internal systems.

Potential use cases include:

- planning and comparing merch projects with an AI assistant in ChatGPT
- creating merch orders from internal tools
- tracking order and production status
- receiving shipment updates
- automating merch workflows with Zapier (triggers, searches, and actions)
- syncing inventory visibility
- connecting approval workflows
- exporting data for procurement or finance
- linking employee gifting flows with HR systems
- automating office or team merch requests

---

## Core concepts

| Concept | Description |
|---|---|
| Product | A curated merch item available through SoMerch. |
| Variant | A specific version of a product, such as size, color, or material. |
| Decoration | Branding method, print area, artwork, and production setup. |
| Quote | A pricing and feasibility request before an order is confirmed. |
| Order | A confirmed merch request with products, quantities, delivery details, and status. |
| Approval | A required internal decision step before production or fulfillment continues. |
| Inventory | Stock stored, reserved, or available for fulfillment. |
| Kit | A grouped set of products shipped together, such as an onboarding pack or event kit. |
| Shipment | A parcel or group of parcels sent to an employee, office, event, or partner. |
| Webhook | An event notification sent from SoMerch to an external system. |

---

## Planned API areas

### Orders

Order-related API resources are planned to support:

- quote requests
- order creation
- order status tracking
- production status
- fulfillment status
- order cancellation rules
- order metadata and tags

See: [`docs/orders.md`](docs/orders.md)

---

### Inventory

Inventory-related API resources are planned to support:

- SKU-level stock visibility
- reserved stock
- low-stock events
- batch information
- warehouse movements
- kit availability

See: [`docs/inventory.md`](docs/inventory.md)

---

### Shipments

Shipment-related API resources are planned to support:

- shipment creation
- carrier tracking
- delivery status
- returned shipments
- multi-address fulfillment
- shipment-level issue handling

See: [`docs/shipments.md`](docs/shipments.md)

---

### Approvals

Approval-related API resources are planned to support:

- approval requests
- approval status
- approver metadata
- budget tags
- department or campaign labels
- audit-friendly approval records

See: [`docs/approvals.md`](docs/approvals.md)

---

### Webhooks

Webhook events help external systems react to changes in SoMerch. Outgoing webhooks are live and power the Zapier triggers.

Live event types:

- `order.created` / `order.status_changed`
- `shipment.created` / `shipment.updated`
- `quote.submitted`

See: [`docs/webhooks.md`](docs/webhooks.md)

---

## Example webhook event

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
