# SoMerch API Authentication

The SoMerch Integrations API uses OAuth 2.0 with per-organization scoping. SoMerch is its own authorization server for third-party clients (including the official Zapier app).

## Token types

| Type | Prefix | Description |
|---|---|---|
| OAuth 2.0 access token | `sat_` | Issued by the token endpoint; valid 8 hours. |
| refresh token | - | Valid 90 days; rotates on every use. |
| API key | `sk_` | Long-lived, org-scoped, revocable from the SoMerch admin console. |
| Supabase user JWT | - | For user-facing tools acting on behalf of a signed-in SoMerch user. |

All `/api/public/v1/*` endpoints accept `Authorization: Bearer <token>`. Both OAuth access tokens and API keys resolve to a single organization.

## OAuth 2.0 authorization code flow (with optional PKCE)

### 1. Redirect the user to the consent screen

```
GET https://somerch.co/oauth/authorize?client_id=...&redirect_uri=...&response_type=code&scope=orders:read%20catalog:read&state=...&code_challenge=...&code_challenge_method=S256
```

Parameters:

| Param | Required | Notes |
|---|---|---|
| `client_id` | yes | Integration client id. |
| `redirect_uri` | yes | Where SoMerch redirects after approval. |
| `response_type` | no | Only `code` is supported. |
| `scope` | no | Space-delimited scopes (see table below). |
| `state` | no | Echoed back in the redirect (CSRF protection). |
| `code_challenge` | no | PKCE S256 challenge (recommended). |
| `code_challenge_method` | no | Only `S256`. |

On approval SoMerch redirects to `redirect_uri?code=<single-use>&state=...`; on denial to `redirect_uri?error=access_denied&state=...`.

### 2. Exchange the code for tokens

```
POST https://somerch.co/api/public/v1/oauth2/token
Content-Type: application/json
```

Body:

```json
{
  "grant_type": "authorization_code",
  "code": "...",
  "redirect_uri": "https://zapier.com/dashboard/auth/oauth/return/AppXXXXXCLIAPI/",
  "client_id": "...",
  "client_secret": "...",
  "code_verifier": "..."     // only when PKCE was used
}
```

Client credentials may also be sent via HTTP Basic auth (Base64 of `client_id:client_secret`).

Response (RFC 6749):

```json
{
  "access_token": "sat_...",
  "token_type": "Bearer",
  "expires_in": 28800,
  "refresh_token": "...",
  "scope": "orders:read catalog:read webhooks:write"
}
```

### 3. Refresh tokens

```
POST https://somerch.co/api/public/v1/oauth2/token
{
  "grant_type": "refresh_token",
  "refresh_token": "...",
  "client_id": "...",
  "client_secret": "..."
}
```

Refresh tokens rotate: always store the newly returned refresh token.

### 4. Verify the connection

```
GET https://somerch.co/api/public/v1/me
Authorization: Bearer sat_...
```

Returns the organization, user (when present), scopes, and a human `label`. The Zapier app calls this after the handshake to label and verify the connection.

### 5. Revoke

```
POST https://somerch.co/api/public/v1/oauth2/revoke
{ "token": "...", "client_id": "...", "client_secret": "..." }
```

Always returns `200` for a well-formed request. The Zapier app calls this when a connection is deleted.

## Scopes

| Scope | Description |
|---|---|
| `orders:read` | Read your orders and their statuses. |
| `shipments:read` | Read your shipments and tracking updates. |
| `quotes:read` | Read quote requests submitted by your organization. |
| `quotes:write` | Submit new quote requests on your behalf. |
| `catalog:read` | Search the SoMerch product catalogue and pricing. |
| `plans:write` | Create merch project plans. |
| `webhooks:write` | Create and remove webhook subscriptions (needed by Zaps). |

The SoMerch Zapier app requests all seven scopes so every trigger, search, and action works out of the box.

## Errors

OAuth endpoints return RFC 6749/7009 error payloads:

```json
{ "error": "invalid_grant", "error_description": "..." }
```

Codes: `invalid_request`, `invalid_client`, `invalid_grant`, `unsupported_grant_type`, `server_error`.

Missing or invalid bearers on `/api/public/v1/*` return `401` with a `WWW-Authenticate` header pointing at `/.well-known/oauth-protected-resource`. Missing scopes return `403`.