# Endpoints, Authentication & Getting Started

> **Source URLs:**
> - https://developer.sage.com/sageactive/quickstart/
> - https://developer.sage.com/sageactive/concepts/environments/
> - https://developer.sage.com/sageactive/concepts/authentication-webserver/
> - https://developer.sage.com/sageactive/concepts/authentication-mobile/
> - https://developer.sage.com/sageactive/concepts/authentication-spa/
> - https://developer.sage.com/sageactive/concepts/bestpractices/
> - https://developer.sage.com/sageactive/guides/devcenter/register-app/

## Getting Started — Bootstrapping a New Project

### Step 1: Subscribe to Sage Active
Subscribe at https://www.sage.com/ for your target legislation (FR, ES, or DE).
You need a Sage ID account associated with a user authorized in the Sage Active production environment.

### Step 2: Access Developer Center
From your Sage Active subscription, access "Your Sage Active" to reach the Developer Center.

### Step 3: Create an App
Register your application in the Developer Center to obtain:
- **Client ID** — Your app identifier
- **Client Secret** — Your app secret (web server apps only; NOT used for mobile/SPA with PKCE)
- **Primary Subscription Key** — Used as `x-api-key` header in every request
- **Secondary Subscription Key** — Backup key for rotation without downtime

### Step 4: Configure Solutions
In the app's Solutions tab, choose which markets your app operates in (FR, ES, DE, or combinations).

### Step 5: Implement OAuth2 Authentication
See the full OAuth2 flows below. Choose the correct flow for your app type.

### Step 6: Make Your First Query
With a valid access token, query the user profile (no X-OrganizationId needed):
```graphql
query { userProfile { id fullName authenticationEmail } }
```

Then list organizations to get X-OrganizationId:
```graphql
query { organizations { nodes { id socialName legislationCode } } }
```

### Step 7: Project Architecture Recommendations
- **Backend-only API access** — Never call Sage Active API from frontend/mobile directly
- **Secrets in environment variables** — Store Client Secret and Subscription Key securely
- **Token storage** — Reserve 2048 bytes for access token and 2048 for refresh token
- **Cache static data** — Countries, currencies, tax groups, document types rarely change

---

## Production Endpoints

| Legislation | GraphQL API URL |
|-------------|----------------|
| **FR** | `https://api.fr.active.sage.com/graphql` |
| **ES** | `https://api.es.active.sage.com/graphql` |
| **DE** | `https://api.de.active.sage.com/graphql` |

> In Postman: `baseAddress` = API URL without `/graphql`

### Authentication URLs (same for all legislations)

| Endpoint | URL |
|----------|-----|
| Authorization | `https://sbcauth.sage.fr/connect/authorize` |
| Token | `https://sbcauth.sage.fr/connect/token` |
| Revocation | `https://sbcauth.sage.fr/connect/revocation` |
| Logout | `https://sbcauth.sage.fr/connect/endsession` |

## Required Headers

| Header | Required | Description |
|--------|----------|-------------|
| `Authorization` | Always | `Bearer {access_token}` |
| `x-api-key` | Always | Primary or secondary subscription key |
| `X-OrganizationId` | Almost always | Organization UUID. NOT needed for: `userProfile`, `organizations`, `localizedEnumValues`, `localizedErrorMessage` |
| `X-Country-Code` | Optional | Legislation code (`es`, `fr`, `de`) for legislation-context queries |
| `X-TenantId` | **DEPRECATED** | No longer required |

---

## OAuth2 Scopes

| Scope | Description |
|-------|-------------|
| `RDSA` | Read Sage Active Data |
| `WDSA` | Write Sage Active Data |
| `offline_access` | Obtain refresh token |

> Only request the permissions your app needs.

## Token Durations

| Token | Duration |
|-------|----------|
| Access Token | **28800 seconds (8 hours)** |
| Authorization Code | **60 seconds** |
| Refresh Token | Server-configured |

---

## OAuth2 Flow 1: Web Server App (with client_secret)

### Step 1 — Authorization Request (GET)
```
GET https://sbcauth.sage.fr/connect/authorize
  ?response_type=code
  &client_id={CLIENT_ID}
  &scope=RDSA%20WDSA%20offline_access
  &redirect_uri={CALLBACK_URL}
  &login_hint={EMAIL_OPTIONAL}
```

| Parameter | Value |
|-----------|-------|
| `response_type` | `code` (always) |
| `client_id` | Your app's Client ID |
| `scope` | Space-separated permissions (`%20` encoded) |
| `redirect_uri` | Callback URL registered in your app |
| `login_hint` | (Optional) Email to pre-fill Sage ID login |

**Success**: HTTP 302 redirect to `redirect_uri` with `code`, `scope`, `iss`, `state` params.

### Step 2 — Token Exchange (POST)
```
POST https://sbcauth.sage.fr/connect/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&code={AUTH_CODE}
&redirect_uri={CALLBACK_URL}
&client_id={CLIENT_ID}
&client_secret={CLIENT_SECRET}
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIs...",
  "expires_in": 28800,
  "refresh_token": "DF6FD016916A1263...",
  "scope": "RDSA WDSA offline_access",
  "token_type": "Bearer"
}
```

### Step 3 — Refresh Token (POST)
```
POST https://sbcauth.sage.fr/connect/token
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token
&refresh_token={REFRESH_TOKEN}
&client_id={CLIENT_ID}
&client_secret={CLIENT_SECRET}
```

> **IMPORTANT**: Do NOT include `scope` parameter when refreshing. The server reuses the original scopes.

### Step 4 — Revoke Refresh Token (POST)
```
POST https://sbcauth.sage.fr/connect/revocation
Content-Type: application/x-www-form-urlencoded

token={REFRESH_TOKEN}
&client_id={CLIENT_ID}
&client_secret={CLIENT_SECRET}
```

### Step 5 — Logout (GET)
```
GET https://sbcauth.sage.fr/connect/endsession
  ?client_id={CLIENT_ID}
  &returnTo={LOGOUT_URL}
```

---

## OAuth2 Flow 2: Mobile/Desktop App (PKCE, no client_secret)

### PKCE Code Verifier & Challenge
```javascript
function base64URLEncode(str) {
  return str.toString('base64').replace(/\+/g, '-').replace(/\//g, '_').replace(/=/g, '');
}
var verifier = base64URLEncode(crypto.randomBytes(32));
var challenge = base64URLEncode(crypto.createHash('sha256').update(verifier).digest());
```

### Step 1 — Authorization Request (GET)
Same as web server but add `code_challenge` and `code_challenge_method=S256`. No `client_secret`.

### Step 2 — Token Exchange (POST)
Same as web server but use `code_verifier` instead of `client_secret`.

### Step 3 — Refresh Token (POST)
```
grant_type=refresh_token&refresh_token={REFRESH_TOKEN}&client_id={CLIENT_ID}
```
> No `client_secret`, no `scope`.

---

## OAuth2 Flow 3: Single Page App (SPA)

Same as Mobile/Desktop (PKCE) but **requires a proxy/backend** for API calls due to CORS policy. The SPA cannot call Sage Active API directly from the browser.

---

## Authorization Errors

| Error | Description |
|-------|-------------|
| `access_denied` | Sage Active refuses authorization |
| `invalid_request` | Missing, invalid, or duplicate parameter |
| `invalid_scope` | Incorrect or insufficient scopes |
| `invalid_grant` | Code expired (>60s), already used, or refresh token revoked |
| `invalid_client` | Client ID or secret incorrect |
| `unauthorized_client` | Client not authorized for this grant_type |
| `server_error` | Internal auth server error |
| `temporarily_unavailable` | Auth server unavailable (wait 10 min) |

---

## Best Practices

- **Rate limit**: 3000 requests per app per minute. Handle HTTP 429 with `Retry-After` header
- **Max parallelism**: 10 concurrent mutations (especially for sequential numbers)
- **Complexity limit**: `maxFieldCost = 650000`. Request only needed fields
- **Cache static data**: Countries, currencies, tax groups, document types, journal types, unit of measurements
- **Field lengths**: Respect max string lengths per field (documented per entity)
- **Enumerations**: Only use valid enum values (documented per entity)
- **Error messages**: Business errors are in English. Use `localizedErrorMessage` query to translate
- **Token storage**: Never store tokens in insecure locations. Use server-side encrypted storage
