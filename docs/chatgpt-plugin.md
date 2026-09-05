# ChatGPT Plugin

SoMerch's ChatGPT plugin connects the SoMerch public MCP server to ChatGPT so you can research products, plan merch projects, and request quotes directly in a chat.

## How to connect

The plugin is approved and listed in the [ChatGPT plugin directory](https://chatgpt.com/plugins/plugin_asdk_app_6a84141dbb1c8191acdb877b8b846b8a), so you can add it directly from there.

To connect it manually:

1. Open ChatGPT and go to **Settings > Connectors > Advanced** (Developer mode).
2. Add a new connector with the raw connector URL:

```
https://somerch.co/mcp
```

3. Save and enable the connector.

After connecting, ChatGPT can browse the SoMerch catalog and use the planner tools on your behalf. No login is required for the catalog and planning tools.

## What you can ask

- "Find sustainable hoodies for a 100-person onboarding pack."
- "Plan a merch kit for a 200-person event in Germany with a 6-week deadline."
- "Compare products by category and quantity-based pricing."
- "Submit a quote request for my plan."
- Works in English, German, and French.

## Tools

| Tool | Purpose |
|---|---|
| `search_merch_products` | Search the catalog by query, category, brand, or collection with quantity-aware price tiers and MOQ. |
| `get_merch_product` | Full product detail (variants, price ladder, branding methods, colors) by id or slug. |
| `plan_merch_project` | Deterministic merch plan with up to three tiered options, deadline and budget feasibility, and EU27 + Switzerland market support. |
| `show_merch_plan` | Re-fetch a stored plan by reference (e.g. `SM-XXXX-XXX`) or id. |
| `submit_quote_request` | Two-phase quote submission (see below). |

## Inline output

Most tools render a visual card (search results, merch plan, quote confirmation) inside ChatGPT, with light/dark theme and EN/DE/FR localization. Plans also include a public widget link (`somerch.co/widget/merch-plan-v1?plan=SM-...`) that stays valid for 24 hours.

## Quote requests

Quote submission is two-phase and server-enforced:

1. The first call returns a confirmation preview with a single-use token (15-minute TTL, bound to the payload).
2. The second call with the identical payload and the token submits the request.

Personal email addresses are rejected, and repeats are idempotent by (plan, option, email). Confirmed quote requests land in the same pipeline as website requests.

## Privacy

- No ChatGPT user identifier is collected or stored.
- Plan data is stored for 24 hours then removed.
- Contact data is only stored after a confirmed quote submission.
- No cookies or tracking; no IP address persisted.

## Related resources

- ChatGPT plugin directory listing: https://chatgpt.com/plugins/plugin_asdk_app_6a84141dbb1c8191acdb877b8b846b8a
- MCP server: [public-mcp.md](public-mcp.md)
- Public docs: [somerch.co/docs/integrations](https://somerch.co/docs/integrations)