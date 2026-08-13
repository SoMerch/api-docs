# Public MCP Server

SoMerch exposes a public MCP (Model Context Protocol) server for AI agent integration.

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

## Server info

| Property | Value |
|---|---|
| Name | `somerch-public` |
| Title | SoMerch Public Catalog |
| Version | `1.0.0` |
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

The public MCP server provides tools for:
- browsing the product catalog
- searching and filtering products
- exploring product categories and variants
- retrieving product details and metadata

## Authentication

The public MCP server is unauthenticated for read-only catalog access. For write operations and authenticated access, use the [staff MCP server](https://somerch.co/staff/mcp) (admin credentials required).

## Integration docs

For detailed integration documentation, visit:
- [somerch.co/docs/integrations](https://somerch.co/docs/integrations)
- OpenAPI spec: [somerch.co/api/public/openapi.json](https://somerch.co/api/public/openapi.json)