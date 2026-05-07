# GraphQL Patterns — Sage Active API V2

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/pagination
> - https://developer.sage.com/sageactive/resources/update
> - https://developer.sage.com/sageactive/resources/dataloader
> - https://developer.sage.com/sageactive/resources/errors
> - https://developer.sage.com/sageactive/guides/hotchocolate/

## General Rules

- All operations use **HTTP POST** (both queries and mutations)
- Always returns **HTTP 200** for well-formed requests (errors in `errors` field)
- Framework: **Hot Chocolate** (GraphQL .NET)
- Complexity limit: **maxFieldCost = 650000**

---

## Pagination (Cursor-based, Relay-style)

- **Default**: 20 records per request
- **Maximum**: 500 records per request

### Forward Pagination
```graphql
query ($first: Int, $after: String) {
  entities(first: $first, after: $after) {
    nodes { id ... }
    pageInfo { endCursor hasNextPage startCursor hasPreviousPage }
    totalCount
  }
}
```
- `first`: Max records to return (1-500)
- `after`: Cursor from previous `pageInfo.endCursor`

### Backward Pagination
- `last`: Max records from end
- `before`: Cursor from previous `pageInfo.startCursor`

### Iteration Example
```
1st call: first:100 (no after) → records 1-100, hasNextPage=true
2nd call: first:100, after:$endCursor → records 101-200
3rd call: first:100, after:$endCursor → records 201-257, hasNextPage=false
```

### Response Formats
```graphql
# nodes format (simpler)
entities { nodes { id name } pageInfo { endCursor hasNextPage } totalCount }

# edges format (includes per-node cursor)
entities { edges { cursor node { id name } } pageInfo { endCursor hasNextPage } totalCount }
```

---

## Filtering (where)

### Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `eq` | Equals | `{ socialName: { eq: "ACME" } }` |
| `neq` | Not equals | `{ status: { neq: "DISABLED" } }` |
| `contains` | Contains (case-insensitive) | `{ socialName: { contains: "corp" } }` |
| `ncontains` | Does not contain | `{ name: { ncontains: "test" } }` |
| `startsWith` | Starts with | `{ code: { startsWith: "C" } }` |
| `nstartsWith` | Does not start with | |
| `endsWith` | Ends with | `{ email: { endsWith: ".com" } }` |
| `nendsWith` | Does not end with | |
| `gt` | Greater than | `{ totalNet: { gt: 1000 } }` |
| `gte` | Greater or equal | `{ documentDate: { gte: "2024-01-01" } }` |
| `lt` | Less than | |
| `lte` | Less or equal | `{ documentDate: { lte: "2024-12-31" } }` |
| `in` | In list | `{ status: { in: ["ENABLED", "DISABLED"] } }` |
| `nin` | Not in list | |

### Logical Operators
```graphql
# AND (implicit — fields at same level)
where: { documentDate: { gte: "2024-01-01" }, status: { eq: "CLOSED" } }

# AND (explicit)
where: { and: [{ documentDate: { gte: "2024-01-01" } }, { documentDate: { lte: "2024-12-31" } }] }

# OR
where: { or: [{ socialName: { contains: "acme" } }, { code: { contains: "acme" } }] }

# Combined AND + OR
where: {
  and: [
    { or: [{ journalType: { code: { eq: "VTE" } } }, { journalType: { code: { eq: "ACH" } } }] },
    { date: { gte: "2024-01-01", lte: "2024-01-31" } }
  ]
}
```

### Nested Filters (DataLoader fields)
```graphql
where: { journalType: { code: { eq: "VTE" } } }
where: { accountingEntryLines: { some: { subAccount: { code: { startsWith: "411" } } } } }
```

---

## Sorting (order)

```graphql
order: [{ socialName: ASC }]
order: [{ documentDate: DESC }, { totalGross: DESC }]
order: [{ journalType: { code: ASC } }, { date: ASC }]
order: [{ code: { value: ASC } }]  # For accountingAccounts (special code structure)
```

> `order` sorts ALL data on the server, not just the current page.

---

## DataLoaders

Enrich queries with related resource fields without additional queries.
Fields marked `DATALOADER` in entity docs can be expanded:

```graphql
query { customers(first: 10) { nodes {
  id socialName
  addresses { country { name isoCodeAlpha2 } }  # DataLoader
  documentType { code name }                       # DataLoader
  defaultAccountingAccount { code { value } name } # DataLoader
} } }
```

Benefits: Solves N+1 problem via batching, per-request caching, reduces overfetching.

---

## Update Patterns

See `references/19-update-patterns.md` for complete update documentation.

### Simple Field Update (Patch-style)
Only send changed fields + id:
```graphql
mutation ($values: CustomerUpdateGLDtoInput!) { updateCustomer(input: $values) { id } }
# Variables: { "values": { "id": "{customerId}", "vatNumber": "FR11123456789" } }
```

### Sub-resource Updates (requestedAction)
| requestedAction | Behavior | Requires id |
|-----------------|----------|-------------|
| `CREATE` | Create new (optional if no id) | No |
| `MODIFY` | Modify existing (optional if id present) | Yes |
| `DELETE` | Delete existing | Yes |

### Collection Replacement (replaceAll)
```json
{ "id": "{customerId}", "replaceAll": ["CONTACTS"], "contacts": [{ "name": "Only Contact" }] }
```

---

## Error Management

| HTTP Code | Type | Description |
|-----------|------|-------------|
| 200 | Business Logic | Rules violation — errors in `errors[].message` |
| 200 | Header Missing | Missing required header (e.g., X-OrganizationId) |
| 200 | Complexity Exceeded | Query exceeds `maxFieldCost: 650000` |
| 400 | Validation | Invalid field name or type |
| 401 | Authorization | Invalid/expired token, missing x-api-key |
| 429 | Rate Limit | 3000 req/min exceeded. Use `Retry-After` header |
| 500 | Server Error | Internal error |

### Business Error Example (200)
```json
{
  "errors": [{ "message": "global.businessErrors.documentIdAlreadyExistInOrganization" }],
  "data": { "createCustomer": null }
}
```

### Translate Error Messages
```graphql
query { localizedErrorMessage(input: {
  languageCode: "es-ES",
  errorKey: "global.businessErrors.documentIdAlreadyExistInOrganization"
}) { errorKey localizedMessage } }
```
