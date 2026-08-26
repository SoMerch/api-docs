# Public MCP Server

SoMerch exposes a public MCP (Model Context Protocol) server for AI agent integration. It is also the connector behind the SoMerch **ChatGPT plugin**.

## Endpoint

```
https://somerch.co/mcp
```

## Discovery

The MCP server can be discovered through standard MCP discovery mechanisms:

| Resource | URL |
|---|---|
| MCP server card (SEP-1649) | `https://somerch.co/.well-known/mcp/server-card.json` |
| Agent skills (WebMCP) | `https://somerch.co/.well-known/agent-skills/webmcp/SKILL.md` |
| Agent skills (API catalog) | `https://somerch.co/.well-known/agent-skills/api-catalog/SKILL.md` |
| OpenAI apps domain challenge | `https://somerch.co/.well-known/openai-apps-challenge` |

## Server info

| Property | Value |
|---|---|
| Name | `somerch-public` |
| Title | SoMerch Public Merch Planner |
| Version | `2.6.6` |
| Origin | `https://somerch.co` |

## How to connect

Add the MCP server to any MCP-compatible client:

```json
{
  "mcpServers": {
    "somerch-public": {
      "url": "https://somerch.co/mcp"
    }
  }
}
```

Supported clients include Claude, ChatGPT, Cursor, OpenCode, and any other MCP-compatible tool.

## Available tools

The public MCP server provides the following tools:

| Tool | Purpose |
|---|---|
| `search_merch_products` | Search the catalog by query, category, brand, or collection with quantity-aware pricing tiers and MOQ. |
| `get_merch_product` | Retrieve full product detail (variants, price ladder, branding methods, colors) by id or slug. |
| `plan_merch_project` | Generate a deterministic merch plan with up to three tiered options, deadline and budget feasibility, and market support for the EU27 and Switzerland. |
| `show_merch_plan` | Re-fetch a stored merch plan by plan reference (e.g. `SM-XXXX-XXX`) or id. |
| `submit_quote_request` | Submit a quote request in two phases: first call returns a confirmation preview, the second call with the matching confirmation token submits the request. |

Every tool returns an inline rendering (Skybridge widget) when used from a supporting client, with light/dark theme and EN/DE/FR localization.

## Authentication

The public MCP server is unauthenticated for read-only catalog access. Quote requests are protected by a server-side two-phase confirmation flow, an email idempotency guard, and a 15-minute single-use confirmation token. For write operations and authenticated admin access, use the [staff MCP server](https://somerch.co/staff/mcp) (admin credentials required).

## ChatGPT plugin

SoMerch's ChatGPT plugin connects to this MCP server. In ChatGPT, add SoMerch as a connector and ask for product recommendations, comparisons, merch plans, feasibility checks, and quote requests directly in chat. See [ChatGPT plugin](chatgpt-plugin.md) for setup steps.

## Integration docs

For detailed integration documentation, visit:
- [somerch.co/docs/integrations](https://somerch.co/docs/integrations)
- OpenAPI spec: [somerch.co/api/public/openapi.json](https://somerch.co/api/public/openapi.json)