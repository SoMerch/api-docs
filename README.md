# SoMerch API Docs

Developer-oriented documentation for the SoMerch platform.

SoMerch is a software-powered corporate merch platform and service partner for distributed companies across Europe. The platform connects product curation, order management, inventory visibility, warehousing, approvals, fulfillment, and EU-wide shipping.

This repository documents SoMerch's API concepts, as well as the live public MCP server and REST API.

---

## Status

SoMerch offers both a **public MCP server** and a **public REST API** that are live and available for integration.

| Resource | Endpoint | Status |
|---|---|---|
| Public MCP server | `somerch.co/mcp` | **Live** |
| MCP server card | `somerch.co/.well-known/mcp/server-card.json` | **Live** |
| Agent skills discovery | `somerch.co/.well-known/agent-skills/` | **Live** |
| REST API OpenAPI spec | `somerch.co/api/public/openapi.json` | **Live** |
| REST API base | `somerch.co/api/public/v1/` | **Live** |
| Integration docs | `somerch.co/docs/integrations` | **Live** |

The documentation in this repository describes platform concepts, planned API resources, webhook events, and example data structures. For the full OpenAPI specification and live integration docs, visit the links above.

For integration requests, contact the SoMerch team through [somerch.co](https://somerch.co).

---

## Live resources

### MCP server

SoMerch exposes a public MCP (Model Context Protocol) server for AI agent integration. Clients can connect to `somerch.co/mcp` to browse the catalog, discover products, and explore merch data.

See: [`docs/public-mcp.md`](docs/public-mcp.md)

### REST API

SoMerch provides a public REST API at `/api/public/v1/` with OAuth authentication, webhooks, events, jobs, actions, and connections management.

See: [`docs/public-api.md`](docs/public-api.md)

---

## What this API is designed for

The SoMerch API is intended to help companies connect merch operations with their internal systems.

Potential use cases include:

- creating merch orders from internal tools
- tracking order and production status
- syncing inventory visibility
- receiving shipment updates
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

Webhook events are planned to help external systems react to changes in SoMerch.

Example event categories:

- order events
- approval events
- inventory events
- shipment events
- fulfillment events

See: [`docs/webhooks.md`](docs/webhooks.md)

---

## Example webhook event

```json
{
  "event": "shipment.updated",
  "created_at": "2026-06-05T10:30:00Z",
  "data": {
    "shipment_id": "shp_12345",
    "order_id": "ord_98765",
    "status": "in_transit",
    "destination_country": "Germany",
    "carrier": "DHL",
    "tracking_number": "JD014600006789000000",
    "tracking_url": "https://example-carrier.com/track/JD014600006789000000"
  }
}
