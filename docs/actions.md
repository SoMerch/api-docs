# SoMerch Actions Reference

All actions are invoked through the central dispatcher:

```
POST https://somerch.co/api/public/v1/actions/{actionKey}
Authorization: Bearer <token>
Content-Type: application/json
Idempotency-Key: <opaque>     # optional, 24-hour TTL
```

Request body:

```json
{ "input": { ... } }
```

Successful response:

```json
{ "ok": true, "data": { ... }, "correlation_id": "..." }
```

Failure response:

```json
{ "ok": false, "error": { "code": "...", "message": "...", "retryable": false }, "correlation_id": "..." }
```

HTTP status is `200` for success, `400` for validation or permanent failures, `502` for retryable upstream failures, `403` for missing scope, `404` for unknown actions.

The SoMerch Zapier app uses the `orders.*`, `catalog.*`, `quotes.submit`, and `plans.create` actions.

---

## system.ping

Verifies auth and connectivity.

```bash
curl -X POST https://somerch.co/api/public/v1/actions/system.ping \
  -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" \
  -d '{ "input": { "message": "hello" } }'
```

Input:

| Field | Type | Required | Notes |
|---|---|---|---|
| `message` | string | no | Up to 500 chars. |

Output:

```json
{ "message": "hello", "organization_id": "...", "caller": "...", "received_at": "..." }
```

---

## orders.list

List recent orders for the caller's organization, newest first.

Input:

| Field | Type | Required | Notes |
|---|---|---|---|
| `limit` | integer | no | 1-200, default 50. |
| `status_id` | string (uuid) | no | Filter by order status id. |

Output: `{ "orders": [ ... ] }`

Scope: `orders:read`.

---

## orders.get

Fetch a single order (org-scoped).

Input:

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string (uuid) | yes | Order id. |

Output: the full order record.

Scope: `orders:read`.

---

## catalog.search_products

Search the published SoMerch catalogue. Quantity-aware pricing: pass `quantity` to filter and report the applicable volume tier. Same engine as the ChatGPT `search_merch_products` tool.

Input:

| Field | Type | Required | Notes |
|---|---|---|---|
| `query` | string | no | Free text matched against product name, SKU, and brand. |
| `categorySlugs` | string[] | no | e.g. `["drinkware"]`, max 10. |
| `brandSlugs` | string[] | no | Max 10. |
| `collectionSlugs` | string[] | no | e.g. `["welcome-kits"]`, max 10. |
| `quantity` | integer | no | Planned quantity per SKU; selects the volume tier. |
| `maxEstimatedUnitPrice` | object | no | `{ amount, currency: "EUR" }`. |
| `excludeApparel` | boolean | no | Skip apparel categories. |
| `limit` | integer | no | 1-20, default 8. |
| `lang` | string | no | `en` \| `de` \| `fr`, default `en`. |

Output: `{ total, products: [productCard], appliedFilters, warnings, lang }`.

Scope: `catalog:read`.

---

## catalog.get_product

Fetch one product with full description, colour variants, and the volume price ladder.

Input:

| Field | Type | Required | Notes |
|---|---|---|---|
| `productId` | string (uuid) | no* | Stable product id. |
| `slug` | string | no* | Canonical or localized slug. |
| `lang` | string | no | `en` \| `de` \| `fr`. |

At least one of `productId` or `slug` is required. Output includes `warnings` and `lang`.

Scope: `catalog:read`.

---

## quotes.submit

Submit a quote request through the shared SoMerch public pipeline (same code path as the website form and the ChatGPT plugin `submit_quote_request` tool). Idempotent by `(plan_id, selected_option_id, email)`.

Input:

| Field | Type | Required | Notes |
|---|---|---|---|
| `plan_id` | string (uuid) | no | Plan id (or use the plan reference; the backend resolves both). |
| `selected_option_id` | string | no | `cost_conscious` \| `balanced` \| `premium`. |
| `company_name` | string | yes | |
| `first_name` | string | yes | |
| `last_name` | string | no | Default `""`. |
| `email` | string (email) | yes | Personal email addresses are rejected. |
| `phone` | string | no | |
| `quantity` | integer | no | 1-1,000,000. |
| `target_budget` | number | no | EUR. |
| `target_date` | string | no | `YYYY-MM-DD`. |
| `note` | string | no | Max 4000 chars. |
| `newsletter_subscribe` | boolean | no | |
| `consent_terms` | boolean | yes | Must be `true`. |

Output:

```json
{ "requestId": "...", "orderId": "...", "orderName": "...", "status": "...", "organizationId": "..." }
```

Scope: `quotes:write`.

---

## plans.create

Plan a merch project from a goal, audience, quantity, and optional budget/deadline. Returns up to three deterministic option tiers, the plan reference (e.g. `SM-7QK4-8P2`), and a shareable plan URL. Creates no order and commits to no price. Same engine as the ChatGPT plugin `plan_merch_project` tool.

Input:

| Field | Type | Required | Notes |
|---|---|---|---|
| `goal` | string | yes | 3-500 chars. |
| `audience` | string | no | Max 200 chars. |
| `quantity` | integer | yes | Total recipients, 1-1,000,000 (min order quantity is 25 pcs). |
| `budgetPerUnit` | object | no | `{ amount, currency: "EUR" }`. |
| `totalBudget` | object | no | `{ amount, currency: "EUR" }`. |
| `deadline` | string | no | `YYYY-MM-DD`. |
| `categorySlugs` | string[] | no | Max 10. |
| `brandSlugs` | string[] | no | Max 10. |
| `collectionSlugs` | string[] | no | Max 10. |
| `excludeApparel` | boolean | no | |
| `sustainabilityPreference` | string | no | `required` \| `preferred` \| `no_preference`. |
| `preferredColors` | string[] | no | e.g. `["red"]`, matched multilingually, max 10. |
| `lang` | string | no | `en` \| `de` \| `fr`. |

Output: `{ planRef, planId, widgetUrl, options[], budgetFeasibility, deadlineSummary, assumptions, warnings, lang }`.

Scope: `plans:write`.

---

## Zapier recommendation (idempotency)

The SoMerch Zapier app sends an `Idempotency-Key` on `quotes.submit` (`zap-<zapId>-<email>-<plan>-<option>`) so Zapier retries never double-fire. Custom integrations should do the same for any state-changing action. Replays return the original response with `X-Idempotent-Replay: true` for up to 24 hours.