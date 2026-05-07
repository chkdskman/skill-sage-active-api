# Aggregations & Lists -- Catalog, Execute, KPIs

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/aggregationoverview
> - https://developer.sage.com/sageactive/resources/aggregationcatalog
> - https://developer.sage.com/sageactive/resources/aggregationexecute
> - https://developer.sage.com/sageactive/resources/aggregations
> - https://developer.sage.com/sageactive/resources/listoverview
> - https://developer.sage.com/sageactive/resources/listcatalog
> - https://developer.sage.com/sageactive/resources/listexecute

## Aggregations Overview

Aggregations in Sage Active allow you to compute totals, counts, averages, and grouped values across accounting, sales, and purchasing data.

There are two approaches to perform aggregations in the API. Understanding the difference is important.

### Approach 1 - Aggregations on Standard Queries (Historical Approach)

This approach consists of adding aggregation parameters directly to existing business queries.

It is powerful but requires:

- Knowledge of the underlying queries
- Knowledge of available fields
- Manual construction of aggregation parameters

This approach is still supported for advanced usages.

### Approach 2 - Aggregation API (Recommended Approach)

The Aggregation API already covers a wide range of business domains. The following aggregation contexts are currently available through the catalog:

- Accounting Entry Lines by Account
- Accounting Entry Lines by Journal
- Sales Quote Lines by Product
- Sales Order Lines by Product
- Sales Delivery Note Lines by Product
- Sales Invoice Lines by Product
- Sales Quotes by Customer
- Sales Orders by Customer
- Sales Delivery Notes by Customer
- Sales Invoices by Customer
- Sales Invoice Payments by Customer
- Purchase Invoices by Supplier
- Purchase Invoice Payments by Supplier

This list may evolve over time as new aggregation capabilities are added.

### Sample of Result

Thanks to the Aggregation API, this result is obtained with a single call to `aggregationExecute`, using only a few parameters:

- An aggregation scope (`entityKey = queryAggregateSalesInvoices`)
- A date range (`dateMin = 2025-01-01`, `dateMax = 2026-12-31`)
- A grouping (`groupByName = Customer`)
- An aggregation (`aggregationType = SUM`)
- A period definition (`periodType = Year`)
- A comparison flag (`compare = true`)
- A Top N limitation (`top = 5`)

The engine automatically handles:

- Period comparison (N / N-1)
- Ranking
- Grouping
- Percentage evolution calculation

The result shows, for each customer in the Top 5, the values for both periods and the percentage evolution.

| Rank | Customer Code | Customer Name | Period N | Period N-1 | Total Net (N) | Total Net (N-1) | Delta % |
|------|---------------|----------------|----------|------------|---------------|-----------------|---------|
| 1 | OPALE | Opale | 2026 | 2025 | 28,570 | 28,970 | -1.38% |
| 2 | RUBIS | Rubis sur ongle | 2026 | 2025 | 14,364 | 342 | +4100% |
| 3 | CARAT | Carat S.a.r.l | 2026 | 2025 | 12,800 | 24,100 | -46.89% |
| 4 | BAGUES | Bague's en or 13 | 2026 | 2025 | 7,950 | 5,750 | +38.26% |
| 5 | DUPOND | Dupond INC | 2026 | 2025 | 7,000 | 7,000 | 0% |

### Aggregation Catalog

This operation exposes a catalog of all available aggregations across Sage Active.

It allows you to discover:

- What can be aggregated
- How data can be grouped
- Which filters are available

This is the entry point for aggregations.

### Aggregation Execute

This operation executes an aggregation based on the definitions returned by `aggregationCatalog`. You no longer need to know the underlying business queries.

### Recommendation

For any new development requiring aggregations, the recommended flow is:

1. Call `aggregationCatalog`
2. Build your aggregation dynamically
3. Call `aggregationExecute`

The historical approach using aggregation parameters on standard queries should only be used for advanced or specific cases.

### Using aggregationCatalog and aggregationExecute Through an MCP Server

One possible use of the `aggregationCatalog` and `aggregationExecute` operations is to expose them as tools through an MCP server.

In this setup, an AI client can dynamically call these tools to discover available aggregations, understand how business metrics are computed, execute the appropriate queries, and translate the results into natural, user-friendly answers.

This removes the need for hardcoded knowledge in the AI layer and allows it to interact directly and reliably with the Aggregation API.

#### Conversation Example

- **User:** Are any of these customers in my Top 10 billed revenue for 2025?
- **AI:** Let me check the available aggregations to find how billed revenue is calculated for Sales Invoices.
- **Tool call - AggregateCatalog:** This tool calls the Aggregation API `aggregationCatalog` operation to discover the appropriate Sales Invoices aggregation and identify that billed revenue can be computed using the invoice Total Net.
- **AI:** Great, I found the correct aggregation. Now I will run it to retrieve the Top 10 customers by billed revenue for 2025.
- **Tool call - AggregateExecute:** This tool calls the Aggregation API `aggregationExecute` operation with the relevant parameters for the year 2025.
- **AI:** Good news! Yes, Bague's en or 13 (Metz) is in your Top 10 customers by billed revenue for 2025. Here is the ranking:
  1. Customer Demo -- 53,107
  2. Opale -- 27,170
  3. Dupond INC -- 7,000
  4. Bague's en or 13 (Metz) -- 2,150
  5. Carat S.a.r.l -- 1,700
  6. Ciselure -- 530
  7. Rubis sur ongle -- 114
  8. Grenat pour toi -- 90
  9. Diamant Vert -- 41

---

## Aggregation Types

| Description | Keyword | Behavior | Field Type |
|-------------|---------|----------|------------|
| Sum | SUM | Returns the sum, default if no keyword is specified | Decimal |
| Average | AVG | Returns the arithmetic mean | Decimal |
| Minimum | MIN | Returns the smallest value | Decimal |
| Maximum | MAX | Returns the largest value | Decimal |
| Distinct Count | DISTINCTCOUNT | Counts the number of unique values for an Id field | Id |
| Count | COUNT | Counts the number of values (use `__count` as field name) | `__count` (constant) |

By default, specifying `["creditAmount", "debitAmount"]` in `aggregateFields` will return the sum of `creditAmount` and `debitAmount` for each group defined in `groupFields`.

**Example with SUM:**

```
aggregateFields: ["creditAmount", "debitAmount"]
```

To use other aggregation types, you can append a `|` followed by the aggregation keyword to each field name. Although it is implicit, you may also choose to explicitly mention SUM.

**Example with MAX:**

```
aggregateFields: ["creditAmount|MAX", "debitAmount|MAX"]
```

To count how many distinct values appear for a specific field in each group (e.g. number of unique customers per product), use DISTINCTCOUNT.

**Example with DISTINCTCOUNT:**

```
aggregateFields: ["customerId|DISTINCTCOUNT"]
```

This returns the number of unique customer IDs in each group, e.g. number of different customers per product.

To count the number of items in each group (rather than aggregating a specific field), use the special keyword `__count`. This is different from other aggregations, as it does not apply to a specific field but rather to the number of entries per group.

**Examples with COUNT:**

```
aggregateFields: ["__count"]
```

You can also combine `__count` with other aggregations:

```
aggregateFields: ["__count", "creditAmount|MAX", "customerId|DISTINCTCOUNT"]
```

**Example for a global COUNT (without groupFields):**

When you want to get a total count without grouping, you must include `id` in the node.

Example: total number of customers

```graphql
query {
  customers {
    edges {
      node {
        id
      }
    }
    aggregate(
      aggregateFields: ["__count"]
    ) {
      aggregates { value }
    }
  }
}
```

### Grouping Data

The `groupFields` array, which specifies the fields used for grouping results, is optional.

If `groupFields` is defined, the query must also specify `groupValues` in the selection set to retrieve the grouping information:

```graphql
groupValues {
  name
  value
}
```

**Example: grouping by subAccountId:**

```
groupFields: ["subAccountId"]
```

If `groupFields` is not defined, the aggregation is computed on the entire dataset. This allows retrieving global indicators such as the total turnover of a customer without requiring any grouping by date or other dimensions.

In this case, the `groupValues` block cannot be used because there is no grouping information to return.

For date-based grouping, users can define time intervals by using the `|` separator. The resulting grouped date value will always be expressed in the format `YYYY-MM-DDT00:00:00.000Z`, where the day component is always set to 01 for Month, Quarter, and Year groupings.

| Grouping Type | Example Value |
|---------------|-------------------------------|
| Day | 2024-03-30T00:00:00.000Z |
| Week | 2024-03-30T00:00:00.000Z |
| Month | 2024-03-01T00:00:00.000Z |
| Quarter | 2024-07-01T00:00:00.000Z |
| Year | 2024-01-01T00:00:00.000Z |

Date grouping is one of the possible elements inside the `groupFields` array, meaning it can be combined with other fields for more granular aggregations.

**Example: grouping by subAccountId and then by monthly period:**

```
groupFields: ["subAccountId", "accountingEntry.date|Month"]
```

### Ordering Data

The `orderFields` array is optional and allows sorting of aggregated results based on specific fields.

In the aggregate block, each field can be ordered in ascending (ASC) or descending (DESC) order using the `|` separator.

For example, ordering by totalNet in descending order:

```
orderFields: ["totalNet|DESC"]
```

When using a count aggregation with `__count`, you can also sort by the number of elements in each group:

```
orderFields: ["__count|DESC"]
```

This is particularly useful when you want to retrieve the most frequent groups, such as the top 10 customers by number of documents or the most used products.

This feature is particularly useful when retrieving Top N results. For example, to get the top 3 customers by totalNet in descending order, limiting the number of records returned by the API, you can combine `orderFields` with `first`:

```graphql
salesQuotes(
  first: 3
  ...){
  ...
  aggregate(
    aggregateFields: ["totalNet", "totalLiquid"],
    groupFields: ["customerId", "documentDate|Quarter"],
    orderFields: ["totalNet|DESC"]
  ) {
    groupValues { value }
    aggregates { value }
  }
}
```

This ensures that only the top 3 customers by totalNet are returned, reducing the volume of data processed and making API responses more efficient.

> **Caution:** `orderFields` and `first` can only be used to sort and retrieve a Top N when the expected result is a direct aggregation based on the fields specified in `groupFields`.
>
> For example, if we group by `customerId` and want to retrieve the top 3 customers with the highest totalNet in descending order, `orderFields` and `first` can be used effectively.
>
> However, if an additional aggregation needs to be applied after the API has performed its aggregation, such as grouping customers by country, then it will not be possible to limit the result to a Top N at the API level.
>
> This is because we would need 100% of the customer-level aggregations first in order to compute the country-level aggregation locally.
>
> In such cases, `orderFields` also becomes irrelevant, as the data must be re-aggregated and re-sorted locally after retrieval.

### Retrieving Aggregated Results

The API processes the request and returns aggregated data structured by the specified groups. The aggregated results may contain:

- **groupValues:** The values of the fields used for grouping, each containing a `name` (field name) and a `value` (grouped value).
- **aggregates:** The computed sums, averages, or counts for each group, each containing a `name` (aggregation type or field name) and a `value` (computed result).

Here is an example of a query that returns the names and values of both grouped and aggregated fields:

```graphql
{
  aggregate(
    aggregateFields: ["debitAmount", "creditAmount"],
    groupFields: ["subAccountId", "accountingEntry.date|Quarter"],
    orderFields: ["debitAmount|DESC"]
  ) {
    groupValues { name value }
    aggregates { name value }
  }
}
```

**Example Response:**

This response shows aggregated values grouped by subAccountId and by month, where each group contains the computed creditAmount and debitAmount.

```json
{
  "data": {
    "accountingEntryLines": {
      "edges": [],
      "aggregate": [
        {
          "groupValues": [
            {
              "name": "subAccountId",
              "value": "fe5ddebf-92b5-4d6c-b56b-82efeefd95f6"
            },
            {
              "name": "accountingEntry.date|Month",
              "value": "2024-03-01T00:00:00.000Z"
            }
          ],
          "aggregates": [
            {
              "name": "creditAmount",
              "value": "6000"
            },
            {
              "name": "debitAmount",
              "value": "0"
            }
          ]
        }
      ]
    }
  }
}
```

Alternatively, for a simplified definition, we could simply mention that `groupValues` and `aggregates` return values:

```graphql
{
  groupValues { value }
  aggregates { value }
}
```

### Pagination

Pagination works the same way as in a standard query, as explained in the pagination documentation. However, it applies to aggregated results rather than the detailed queried data used for the calculations.

**Retrieving the Full Aggregation Results:**

To retrieve the full set of aggregation results, the query must be executed multiple times, assigning `$endCursor` with the `endCursor` value from the previous response, until `hasNextPage` is false.

**Pagination Limits:**

The maximum number of aggregation results that can be retrieved in one request is 500 (`first:500`). If `hasNextPage` is true, additional requests should be made using `after: $endCursor`.

Example: If pagination is set to 50 using `first: 50` and the requested filter covers 2000 accounting entry lines, all 2000 lines will be used for aggregations and groupings, but only the first 50 aggregation results will be returned.

```graphql
query ($endCursor: String!) {
  salesInvoices (
    first: 50
    after: $endCursor
    where: {...}
  ) {
    edges {
      node {
        ...
      }
    }
    aggregate(
      aggregateFields: [...],
      groupFields: [...],
      orderFields: [...]
    )
    {
      pageInfo {
        endCursor
        hasNextPage
      }
    }
  }
}
```

### Important Points and Limitations

#### Grouping Restrictions for Child Entities

While it is possible to group by fields from an entity and its main entity, it is not allowed to group by fields from child entities.

For example, grouping by `subAccountId` is valid because it belongs to the main entity. Similarly, grouping by `accountingEntry.date` is also allowed, as `accountingEntry` is the parent entity of `accountingEntryLines`. However, grouping by `subAccount.code` is not possible since `subAccount` is a child entity.

This limitation exists because of data loader optimizations. When executing a query, the system splits it into multiple queries to optimize performance:

- The main query retrieves the primary entity records.
- Separate follow-up queries fetch child entity data, but only for the specific child entities referenced in the query.

In the case of aggregations, child entity data has not yet been retrieved at the time of aggregation. Since the grouping operation happens at the aggregation stage, before the child entity queries are executed, it is not possible to group by a field that has not yet been loaded.

#### Exposing Fields Used in aggregateFields and groupFields

It is mandatory to declare all fields used for aggregation or grouping in the `edges` node.

For example, to aggregate and group:

```graphql
{
  ...
  aggregate(
    aggregateFields: ["creditAmount", "debitAmount"],
    groupFields: ["subAccountId", "accountingEntry.date|Quarter"]
  )
  ...
}
```

You must expose:

```graphql
edges {
  node {
    creditAmount
    debitAmount
    subAccountId
    accountingEntry {
      date
    }
  }
}
```

Conversely, it is not allowed to expose fields in edges that are not used in the aggregation. For example, if `totalNet` is exposed in edges while the aggregation is:

```graphql
{
  ...
  aggregate(
    aggregateFields: ["totalQuantity"],
    groupFields: ["productId", "salesOrder.documentDate|Quarter"]
  )
  ...
}
```

The following error will be returned:

```json
{
  "errors": [
    {
      "message": "totalNet field is not used in the aggregation and should be removed from edges.",
      "extensions": {
        "code": "INVALID_INPUT"
      }
    }
  ]
}
```

#### Understanding totalCount in Aggregations

In a standard query, `totalCount` typically returns the total number of records matching the query, regardless of the number of results returned based on the defined pagination.

However, in the context of aggregations, while `totalCount` remains syntactically valid, its value is not meaningful. This is because aggregations transform and group data, making the raw count of records irrelevant to the aggregated output.

This is not a critical issue, as aggregation logic inherently requires retrieving the entire set of results to be useful. Consequently, the total number of aggregated results can be determined by fully paginating through the aggregation query, as explained in the pagination section.

---

## Aggregation Catalog

### Description

The `aggregationCatalog` operation exposes a ready-to-use list of aggregation capabilities available in Sage Active.

It is designed to simplify the way aggregations are discovered and executed by providing a predefined catalog of possible aggregations, their grouping options, filters, and available value columns.

This approach allows clients to obtain meaningful aggregated results without needing to understand or configure the underlying business queries.

For more advanced or highly specific aggregation scenarios, it is still possible to use the historical aggregation mechanism described in aggregations.

This operation does not return business data. It returns metadata describing how aggregations can be performed across multiple domains such as:

- Accounting
- Sales
- Purchases

This catalog is used to dynamically discover:

- Which aggregations exist
- Which fields can be grouped
- Which values can be aggregated
- Which filters are available
- Which period types are supported

> **Important:** This catalog is the single source of truth for building valid calls to the `aggregationExecute` operation. Each value returned in the catalog must be reused as-is when calling `aggregationExecute`. Any value not explicitly returned by this catalog must be considered invalid. These are not technical codes but the exact expected input values.

### Functionality

- Discover available aggregations
- List all possible groupings
- List available aggregation types
- List available value columns
- List distinct count capabilities
- Filter the catalog by aggregation context

### Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| contextContains | String | No | Optional filter allowing to return only aggregations whose context contains the provided value |

### Response

The service returns the catalog entries in the `definitions` field. Each definition describes how an aggregation can be executed.

The following aggregation contexts are currently available through the catalog:

- Accounting Entry Lines by Account
- Accounting Entry Lines by Journal
- Sales Quote Lines by Product
- Sales Order Lines by Product
- Sales Delivery Note Lines by Product
- Sales Invoice Lines by Product
- Sales Quotes by Customer
- Sales Orders by Customer
- Sales Delivery Notes by Customer
- Sales Invoices by Customer
- Sales Invoice Payments by Customer
- Purchase Invoices by Supplier
- Purchase Invoice Payments by Supplier

This list may evolve over time as new aggregation capabilities are added.

> This operation returns static business metadata that can be filtered using `contextContains`. There is no pagination and no edges/nodes pattern.

### Header

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| x-api-key | Primary or secondary subscription key of your app |

### GraphQL Query

```graphql
query {
  aggregationCatalog(contextContains: "Accounting Entry") {
    definitions {
      entityKey
      context
      aggregationTypes
      periodTypes
      filters
      groupBy { name title1 title2 }
      valueColumns
      distinctCounts
      searchScopes
    }
  }
}
```

### Example Response

```json
{
  "data": {
    "aggregationCatalog": {
      "definitions": [
        {
          "entityKey": "queryAggregateAccountingEntryLines",
          "context": "Accounting Entry Lines by Account",
          "aggregationTypes": ["SUM", "AVG", "MIN", "MAX", "DISTINCTCOUNT", "COUNT"],
          "periodTypes": ["Day", "Week", "Month", "Quarter", "Year"],
          "filters": ["All accounts", "Charges & Revenues", "Charges", "Revenues", "Suppliers & Customers"],
          "groupBy": [
            { "name": "None", "title1": null, "title2": null },
            { "name": "Account Code", "title1": "Account Code", "title2": "Account Name" },
            { "name": "First 2 Digits", "title1": "First 2 Digits", "title2": "" },
            { "name": "First 3 Digits", "title1": "First 3 Digits", "title2": "" }
          ],
          "valueColumns": ["Debit", "Credit"],
          "distinctCounts": ["Accounting Entries", "Accounting Exercises", "Journal Types"],
          "searchScopes": ["CustomerId", "ProductId"]
        },
        {
          "entityKey": "queryAggregateAccountingEntryLinesByJournal",
          "context": "Accounting Entry Lines by Journal",
          "aggregationTypes": ["SUM", "AVG", "MIN", "MAX", "DISTINCTCOUNT", "COUNT"],
          "periodTypes": ["Day", "Week", "Month", "Quarter", "Year"],
          "filters": ["All Journals", "Charges & Revenues", "Charges, Revenues, Financial"],
          "groupBy": [
            { "name": "Journal Code", "title1": "Journal Code", "title2": "Journal Name" },
            { "name": "Journal Type", "title1": "Type", "title2": "" }
          ],
          "valueColumns": ["Debit", "Credit"],
          "distinctCounts": ["Accounting Entries", "Accounts Used", "Accounting Exercises"],
          "searchScopes": ["CustomerId", "ProductId"]
        }
      ]
    }
  }
}
```

### aggregationCatalog Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| contextContains | String | No | Filters aggregation definitions by matching the value against `context`. Optional free-text filter used to restrict the aggregation catalog results based on the aggregation context. When provided, only aggregation definitions whose context contains the given value are returned. Useful to narrow down the catalog to a functional domain (e.g. Accounting, Sales) or simplify discovery when many aggregation definitions are available. This filter applies only to the catalog metadata. It does not affect aggregation execution or business data. |

### aggregationCatalog Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| definitions[] | AggregationDefinition | -- | List of available aggregation catalog definitions |

### Definition Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| entityKey | String | -- | Technical identifier of the aggregation. To reuse as-is as `entityKey` in `aggregationExecute`. |
| context | String | -- | Human-readable description of the aggregation purpose. |
| aggregationTypes[] | [String] | -- | List of allowed aggregation operations to reuse as-is as `aggregationType` in `aggregationExecute` (e.g. SUM, AVG, MIN, MAX, COUNT, DISTINCTCOUNT). |
| periodTypes[] | [String] | -- | Supported time grouping values to reuse as-is as `periodType` in `aggregationExecute` (e.g. Day, Week, Month, Quarter, Year, None). |
| filters[] | [String] | -- | Predefined business filters that can be reused as-is as `filterName` in `aggregationExecute`. |
| groupBy[] | [GroupByItem] | -- | Available business grouping definitions to reuse as-is as `groupByName` in `aggregationExecute`. The special value "None" represents a grand total without business grouping. |
| valueColumns[] | [String] | -- | Value columns that can be aggregated and reused as-is as `valueColumn1` or `valueColumn2` in `aggregationExecute`. |
| distinctCounts[] | [String] | -- | Metrics that can be used when `aggregationType` is DISTINCTCOUNT, as-is as `distinctCountName` in `aggregationExecute`. |
| searchScopes[] | [String] | -- | Optional list of supported entity scopes for ID-based restriction. When present, these values indicate that the aggregation can be restricted using the `search` (and optional `searchScope`) parameter in `aggregationExecute`. |

### GroupBy Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| name | String | -- | Value to reuse as-is in `aggregationExecute` |
| title1 | String | -- | First display label |
| title2 | String | -- | Second display label |

---

## Aggregation Execute

### Description

The `aggregationExecute` operation executes an aggregation based strictly on the definitions returned by `aggregationCatalog`.

This operation does not require any knowledge of the underlying business queries. Instead, it relies entirely on the metadata exposed by the catalog.

Most parameters provided to this operation must come from the values returned by `aggregationCatalog`. This guarantees:

- Valid aggregation parameters
- Consistent grouping and filtering
- Safe execution without invalid fields
- The ability to build aggregations dynamically without prior API knowledge

### Sample of Result

Thanks to the Aggregation API, this result is obtained with a single call to `aggregationExecute`, using only a few parameters:

- An aggregation scope (`entityKey = queryAggregateSalesInvoices`)
- A date range (`dateMin = 2025-01-01`, `dateMax = 2026-12-31`)
- A grouping (`groupByName = Customer`)
- An aggregation (`aggregationType = SUM`)
- A period definition (`periodType = Year`)
- A comparison flag (`compare = true`)
- A Top N limitation (`top = 5`)

The engine automatically handles:

- Period comparison (N / N-1)
- Ranking
- Grouping
- Percentage evolution calculation

The result shows, for each customer in the Top 5, the values for both periods and the percentage evolution.

| Rank | Customer Code | Customer Name | Period N | Period N-1 | Total Net (N) | Total Net (N-1) | Delta % |
|------|---------------|----------------|----------|------------|---------------|-----------------|---------|
| 1 | OPALE | Opale | 2026 | 2025 | 28,570 | 28,970 | -1.38% |
| 2 | RUBIS | Rubis sur ongle | 2026 | 2025 | 14,364 | 342 | +4100% |
| 3 | CARAT | Carat S.a.r.l | 2026 | 2025 | 12,800 | 24,100 | -46.89% |
| 4 | BAGUES | Bague's en or 13 | 2026 | 2025 | 7,950 | 5,750 | +38.26% |
| 5 | DUPOND | Dupond INC | 2026 | 2025 | 7,000 | 7,000 | 0% |

### Functionality

- Execute aggregations across Accounting, Sales, and Purchases
- Group data dynamically
- Apply predefined business filters
- Aggregate one or two value columns
- Perform distinct counts
- Compare periods automatically
- Limit results using Top N and Others logic

### Important Principle

`aggregationExecute` must always be used after calling `aggregationCatalog`.

The catalog defines the exact allowed values for:

- `entityKey`
- `aggregationType`
- `periodType`
- `filterName`
- `groupByName`
- `valueColumn1`, `valueColumn2`
- `distinctCountName`

If a value is not present in `aggregationCatalog`, it is invalid for `aggregationExecute`.

### Result Limits

The number of rows returned by `aggregationExecute` is intentionally limited to a maximum of 500 results. This operation is designed for analytical aggregations, not for data browsing.

- Grouping dimensions
- Top N logic
- Business filters
- Time periods

As a consequence:

- Pagination is not supported
- Returning more than 500 rows would indicate a misuse of the aggregation endpoint
- For large datasets or record-level access, list-based APIs must be used instead

### Header

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId (deprecated) | Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### GraphQL Query

```graphql
query ($input: AggregationExecuteDtoInput!) {
  aggregationExecute(input: $input) {
    meta {
      entityKey
      context
      aggregationType
      periodType
      dateMin
      dateMax
      compare
      top
      includeOthers
      othersLabel
      filterName
      groupByName
      valueColumn1
      valueColumn2
      distinctCountName
      search
      searchScope
    }
    rows {
      groupValue
      secondValue
      period
      value1
      value2
      periodN
      periodN_1
      valueN
      valueN_1
      deltaPercent
    }
  }
}
```

### Example Variables

```json
{
  "input": {
    "entityKey": "queryAggregateSalesInvoices",
    "aggregationType": "SUM",
    "periodType": "Month",
    "dateMin": "2025-01-01",
    "dateMax": "2025-12-31",
    "compare": false,
    "top": 5,
    "includeOthers": false,
    "othersLabel": "Others",
    "filterName": "All Customers",
    "groupByName": "Customer",
    "valueColumn1": "Total Net",
    "valueColumn2": "Total Liquid",
    "distinctCountName": "Sales Invoices"
  }
}
```

### Example Response

```json
{
  "meta": {
    "entityKey": "queryAggregateSalesInvoices",
    "context": "Sales Invoices by Customer",
    "aggregationType": "SUM",
    "periodType": "Year",
    "dateMin": "2025-01-01",
    "dateMax": "2025-12-31",
    "compare": false,
    "top": 5,
    "includeOthers": false,
    "othersLabel": "Others",
    "filterName": "All Customers",
    "groupByName": "Customer",
    "valueColumn1": "Total Net",
    "valueColumn2": "Total Liquid",
    "distinctCountName": "Sales Invoices",
    "search": null,
    "searchScope": null
  },
  "rows": [
    {
      "groupValue": "1",
      "secondValue": "Customer Demo",
      "period": "2025",
      "value1": 54917,
      "value2": 65900.4,
      "periodN": null,
      "periodN_1": null,
      "valueN": null,
      "valueN_1": null,
      "deltaPercent": null
    },
    {
      "groupValue": "ANDERSSON",
      "secondValue": "Andersson AB",
      "period": "2025",
      "value1": 35200,
      "value2": 35200,
      "periodN": null,
      "periodN_1": null,
      "valueN": null,
      "valueN_1": null,
      "deltaPercent": null
    },
    {
      "groupValue": "OPALE",
      "secondValue": "Opale",
      "period": "2025",
      "value1": 28970,
      "value2": 28970,
      "periodN": null,
      "periodN_1": null,
      "valueN": null,
      "valueN_1": null,
      "deltaPercent": null
    },
    {
      "groupValue": "CARAT",
      "secondValue": "Carat S.a.r.l",
      "period": "2025",
      "value1": 24100,
      "value2": 28920,
      "periodN": null,
      "periodN_1": null,
      "valueN": null,
      "valueN_1": null,
      "deltaPercent": null
    },
    {
      "groupValue": "DUPOND",
      "secondValue": "Dupond INC",
      "period": "2025",
      "value1": 7000,
      "value2": 7000,
      "periodN": null,
      "periodN_1": null,
      "valueN": null,
      "valueN_1": null,
      "deltaPercent": null
    }
  ]
}
```

**Top 5 Results Table (no comparison):**

| Rank | Customer Code | Customer Name | Period | Total Net | Total Liquid |
|------|---------------|----------------|--------|-----------|--------------|
| 1 | 1 | Customer Demo | 2025 | 54,917 | 65,900 |
| 2 | ANDERSSON | Andersson AB | 2025 | 35,200 | 35,200 |
| 3 | OPALE | Opale | 2025 | 28,970 | 28,970 |
| 4 | CARAT | Carat S.a.r.l | 2025 | 24,100 | 28,920 |
| 5 | DUPOND | Dupond INC | 2025 | 7,000 | 7,000 |

By simply setting `includeOthers` to true, the API automatically adds an additional row that represents the revenue of all customers outside the Top 5. The Others row is automatically calculated by the engine and represents the aggregated totals of all remaining customers not included in the Top N.

| Rank | Customer Code | Customer Name | Period | Total Net | Total Liquid |
|------|---------------|----------------|--------|-----------|--------------|
| 1 | 1 | Customer Demo | 2025 | 54,917 | 65,900 |
| 2 | ANDERSSON | Andersson AB | 2025 | 35,200 | 35,200 |
| 3 | OPALE | Opale | 2025 | 28,970 | 28,970 |
| 4 | CARAT | Carat S.a.r.l | 2025 | 24,100 | 28,920 |
| 5 | DUPOND | Dupond INC | 2025 | 7,000 | 7,000 |
| -- | Others | | | 13,875 | 16,650 |

With `compare = true` and a date range covering two full years, the engine automatically compares:

- **Period N** -- the most recent period inside the range
- **Period N-1** -- the equivalent previous period

The result shows, for each customer in the Top 5, the values for both periods and the percentage evolution.

| Rank | Customer Code | Customer Name | Period N | Period N-1 | Total Net (N) | Total Net (N-1) | Delta % |
|------|---------------|----------------|----------|------------|---------------|-----------------|---------|
| 1 | OPALE | Opale | 2026 | 2025 | 28,570 | 28,970 | -1.38% |
| 2 | RUBIS | Rubis sur ongle | 2026 | 2025 | 14,364 | 342 | +4100% |
| 3 | CARAT | Carat S.a.r.l | 2026 | 2025 | 12,800 | 24,100 | -46.89% |
| 4 | BAGUES | Bague's en or 13 | 2026 | 2025 | 7,950 | 5,750 | +38.26% |
| 5 | DUPOND | Dupond INC | 2026 | 2025 | 7,000 | 7,000 | 0% |

### aggregationExecute Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| entityKey | String | Yes | Aggregation definition identifier. Must match an `entityKey` returned by `aggregationCatalog`. |
| aggregationType | String | Yes | Aggregation operation to apply (e.g. SUM, AVG, MIN, MAX, COUNT, DISTINCTCOUNT). Must match one of the aggregation types returned by `aggregationCatalog`. COUNT and DISTINCTCOUNT ignore `valueColumn1`. DISTINCTCOUNT requires `distinctCountName`. All sorting and Top N logic are always based on `valueColumn1`. |
| periodType | String | Yes | Time grouping granularity (e.g. Day, Week, Month, Quarter, Year, None). Must match one of the period types returned by `aggregationCatalog`. When not None, results are grouped by period. When None, no time grouping is applied. |
| dateMin | String | Yes | Start date of the reference period (ISO 8601, e.g. `2024-01-01`). When `groupByName` is set and (`top > 0` or `compare = true`) and `periodType` is not None, the engine automatically recalculates dateMin to align with periodType. When `groupByName = None`, dateMin is used as-is. |
| dateMax | String | Yes | End date of the reference period (ISO 8601). Used as the reference date. When `groupByName` is set, date recalculation rules apply. When `groupByName = None`, dateMax is used as-is. |
| compare | Boolean | No | Enable period comparison. When true and `groupByName` is set, compares period N with period N-1 per business entity. Response exposes `periodN`, `periodN_1`, `valueN`, `valueN_1`, `deltaPercent`. When `groupByName = None`, dates are not recalculated and all periods between dateMin and dateMax are returned. |
| top | Int | No | Top N groups. `-1` = no limit, all groups returned without ranking. `0` = all groups returned, ordered by valueColumn1 descending. `> 0` = only Top N groups returned, ordered by valueColumn1 descending. Sorting is always on valueColumn1 only. |
| includeOthers | Boolean | No | Add an aggregated Others row (only when `top > 0`). |
| othersLabel | String | No | Label for the Others row. Default is "Others". |
| filterName | String | No | Business filter applied before aggregation. Must match exactly one filter name returned by `aggregationCatalog`. To restrict to a specific entity, use `search` instead. |
| groupByName | String | No | Business grouping dimension (e.g. Customer, Product, Account). Must match a `groupBy` name from `aggregationCatalog`. When set to "None", behaves as a pure time-series or grand-total aggregation with no entity ranking. |
| valueColumn1 | String | Conditional | Main value used for aggregation and ranking. Required unless `aggregationType` is COUNT or DISTINCTCOUNT. Must match a `valueColumn` name from `aggregationCatalog`. Used for all sorting and Top N logic. |
| valueColumn2 | String | No | Optional complementary value. Returned as-is in the result but never used for sorting or ranking. |
| distinctCountName | String | Conditional | Required only when `aggregationType = DISTINCTCOUNT`. Must match a `distinctCount` value from `aggregationCatalog`. |
| search | String | No | Restricts the aggregation to a specific entity id (customer, product, supplier). Applied on top of the selected `filterName`. |
| searchScope | String | No | Required when multiple search scopes are supported. Values exposed by `searchScopes` in `aggregationCatalog`. Can be omitted when only one searchScope is available. |

#### Grouping Logic (groupByName x periodType)

The aggregation engine uses two independent grouping dimensions:

- A business dimension defined by `groupByName`
- A time dimension defined by `periodType`

These dimensions combine to determine the structure and number of rows returned. There are four possible combinations:

| groupByName | periodType | Result |
|-------------|------------|--------|
| Set (not None) | Set (not None) | One row per business entity and per period (e.g. revenue per customer, per month) |
| Set (not None) | None | One row per business entity, without time breakdown (e.g. total revenue per customer) |
| None | Set (not None) | One row per period, without business breakdown (e.g. global revenue per month) |
| None | None | A single grand total row, no grouping |

### aggregationExecute Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| meta | AggregationExecuteMeta | -- | Echo of the executed aggregation parameters. Values may differ from input if certain parameters were inconsistent. The meta section always reflects the final parameters actually applied during execution. |
| rows | AggregationExecuteRow | -- | Aggregated result rows |

### Meta Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| entityKey | String | -- | Executed aggregation definition identifier |
| context | String | -- | Human-readable aggregation context |
| aggregationType | String | -- | Aggregation operation actually applied |
| periodType | String | -- | Time grouping used during execution |
| dateMin | String | -- | Effective start date used by the engine |
| dateMax | String | -- | Effective end date used by the engine |
| compare | Boolean | -- | Indicates whether period comparison was applied |
| top | Int | -- | Effective Top N value used |
| includeOthers | Boolean | -- | Indicates whether an Others row was added |
| othersLabel | String | -- | Label used for the Others row |
| filterName | String | -- | Business filter applied |
| groupByName | String | -- | Business grouping applied |
| valueColumn1 | String | -- | Main aggregated value |
| valueColumn2 | String | -- | Secondary aggregated value (if any) |
| distinctCountName | String | -- | Distinct count metric (when applicable) |
| search | String | -- | Entity identifier used to restrict results (if any) |
| searchScope | String | -- | Identifier type used for search (if any) |

**Example meta response:**

```json
{
  "meta": {
    "entityKey": "queryAggregateSalesInvoices",
    "context": "Sales Invoices by Customer",
    "aggregationType": "SUM",
    "periodType": "Year",
    "dateMin": "2025-01-01",
    "dateMax": "2025-12-31",
    "compare": false,
    "top": 5,
    "includeOthers": false,
    "othersLabel": "Others",
    "filterName": "All Customers",
    "groupByName": "Customer",
    "valueColumn1": "Total Net",
    "valueColumn2": "Total Liquid",
    "distinctCountName": "Sales Invoices",
    "search": null,
    "searchScope": null
  }
}
```

### Row Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| groupValue | String | -- | Primary grouping value |
| secondValue | String | -- | Optional complementary grouping label (e.g. Customer Name when grouping by Customer) |
| period | String | -- | Period label (when `compare = false`) |
| value1 | Decimal | -- | Aggregated value for valueColumn1 |
| value2 | Decimal | -- | Aggregated value for valueColumn2 |
| periodN | String | -- | Current period label (when `compare = true`) |
| periodN_1 | String | -- | Previous period label (when `compare = true`) |
| valueN | Decimal | -- | Aggregated value for period N |
| valueN_1 | Decimal | -- | Aggregated value for period N-1 |
| deltaPercent | Decimal | -- | Percentage evolution between N and N-1 |

Each row always exposes the same set of fields. Depending on the mode (`compare = false` or `true`), some fields will contain values and others will be null.

- When `compare = false`: `period`, `value1`, and `value2` are populated. The comparison fields (`periodN`, `periodN_1`, `valueN`, `valueN_1`, `deltaPercent`) are null.
- When `compare = true`: The comparison fields are populated. `period`, `value1`, and `value2` are null.

**Sample 1 - Standard aggregation (no comparison):**

```json
{
  "rows": [
    {
      "groupValue": "ANDERSSON",
      "secondValue": "Andersson AB",
      "period": "2025",
      "value1": 35200,
      "value2": 35200,
      "periodN": null,
      "periodN_1": null,
      "valueN": null,
      "valueN_1": null,
      "deltaPercent": null
    }
  ]
}
```

**Sample 2 - Aggregation with period comparison (compare = true):**

```json
{
  "rows": [
    {
      "groupValue": "OPALE",
      "secondValue": "Opale",
      "period": null,
      "value1": null,
      "value2": null,
      "periodN": "2026",
      "periodN_1": "2025",
      "valueN": 28570,
      "valueN_1": 28970,
      "deltaPercent": -1.38
    },
    {
      "groupValue": "RUBIS",
      "secondValue": "Rubis sur ongle",
      "period": null,
      "value1": null,
      "value2": null,
      "periodN": "2026",
      "periodN_1": "2025",
      "valueN": 14364,
      "valueN_1": 342,
      "deltaPercent": 4100
    }
  ]
}
```

---

## Lists Overview

Lists in Sage Active allow you to retrieve business-ready datasets such as customers, sales documents, invoice lines, or open items, using predefined and secured business rules.

They are designed to provide consistent, reusable, and controlled access to business data without exposing the underlying GraphQL schema.

There are two approaches to retrieve lists in the API. Understanding the difference is important.

### Approach 1 - Lists on Standard Queries (Historical Approach)

This approach consists of calling standard GraphQL business queries directly (`customers`, `salesInvoices`, `salesInvoiceLines`, etc.).

It is flexible but requires:

- Knowledge of the underlying GraphQL schema
- Knowledge of relations and nested fields
- Manual construction of filters and ordering
- Strong coupling with the API structure

This approach is still supported for advanced or highly specific use cases.

### Approach 2 - List API (Catalog-Based Approach)

The List API already covers the main business domains. The following business lists are currently available through the catalog:

- Customers
- Suppliers
- Products
- Sales Quotes
- Sales Orders
- Sales Delivery Notes
- Sales Invoices
- Sales Invoice Open Items
- Sales Quote Lines
- Sales Order Lines
- Sales Delivery Note Lines
- Sales Invoice Lines
- Purchase Invoices
- Purchase Invoice Open Items
- Purchase Invoice Lines
- Accounting Entries
- Accounting Entry Lines

This list may evolve over time as new business list definitions are added.

### Sample of Result

Thanks to the List API, this result is obtained with a single call to `listExecute`, using only predefined business parameters:

- A list definition (`entityKey = salesInvoiceLines`)
- A business filter (`filter = Unpaid invoice lines`)
- A sorting rule (`orderBy = Invoice date desc`)

The engine automatically handles:

- Validation of the list definition
- Validation of allowed filters
- Validation of allowed sorting rules
- Execution of the underlying business query
- Pagination and total count

The result returns a business-ready list, without requiring any knowledge of the underlying GraphQL schema.

| Invoice Number | Invoice Date | Customer | Product Code | Quantity | Total Net |
|----------------|-------------|----------|-------------|----------|-----------|
| SI-2026-0145 | 2026-03-12 | Opale | PROD-001 | 2 | 1,200.00 |
| SI-2026-0138 | 2026-03-10 | Rubis sur ongle | PROD-014 | 1 | 850.00 |
| SI-2026-0122 | 2026-03-05 | Bague's en or 13 | PROD-007 | 3 | 2,100.00 |
| SI-2026-0119 | 2026-03-04 | Carat S.a.r.l | PROD-003 | 5 | 3,750.00 |
| SI-2026-0115 | 2026-03-01 | Dupond INC | PROD-021 | 1 | 420.00 |

It provides business-defined lists with predefined filters and sorting rules.

### List Catalog

This operation exposes a catalog of all available business list definitions across Sage Active. It allows you to discover:

- Which lists exist
- Which business filters are available
- Which sorting rules are allowed

This is the entry point for lists. The catalog returns metadata only and does not return business data.

### List Execute

This operation executes a list based on the definitions returned by `listCatalog`. You no longer need to:

- Know the underlying GraphQL queries
- Understand the schema structure
- Build custom filters or order clauses

All inputs are validated against the catalog definitions.

### Recommendation

For any new development requiring lists, the recommended flow is:

1. Call `listCatalog`
2. Select the appropriate list definition
3. Choose an available filter and sorting rule
4. Call `listExecute`

Direct usage of standard GraphQL queries should be reserved for advanced or exceptional cases.

### Using listCatalog and listExecute Through an MCP Server

One possible use of the `listCatalog` and `listExecute` operations is to expose them as tools through an MCP server.

In this setup, an AI client can dynamically:

- Discover available business lists
- Understand allowed filters and sorting rules
- Execute the appropriate list
- Interpret and present the results to the user

This removes the need for hardcoded schema knowledge in the AI layer and ensures that business rules are always respected.

#### Conversation Example

- **User:** Show me all unpaid sales invoices for this year.
- **AI:** Let me check the available lists to see how unpaid sales invoices are defined.
- **Tool call - ListCatalog:** This tool calls the List API `listCatalog` operation to discover the Sales Invoice lists and identify the available business filters.
- **AI:** I found the appropriate list and filter. I will now retrieve the data.
- **Tool call - ListExecute:** This tool calls the List API `listExecute` operation with the selected list, filter, and sorting rule.
- **AI:** Here is the list of unpaid sales invoices for the current year.

---

## List Catalog

### Description

The `listCatalog` operation exposes a ready-to-use catalog of business list definitions available in Sage Active.

It is designed to simplify how lists are discovered, configured, and executed by providing a predefined set of business-driven list capabilities, including:

- Available lists
- Allowed business filters
- Allowed sorting rules
- Associated query models
- Date range support for list execution

This approach allows clients to retrieve consistent and meaningful lists without building custom GraphQL queries or understanding the underlying schema.

Each list definition represents a stable business contract, aligned with Sage Active domains such as:

- Accounting
- Sales
- Purchases
- Master data (Customers, Suppliers, Products)

> This operation does not return business data. It returns metadata describing how lists can be executed using the `listExecute` operation.

### Functionality

- Discover available business lists
- Identify supported filters for each list
- Identify allowed sorting rules
- Understand which business domains are covered
- Determine whether a list supports date range filtering
- Drive generic list screens and exports
- Secure list execution by design

### Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| contextContains | String | No | Optional filter allowing to return only lists whose context contains the provided value |

### Response

The service returns the catalog entries in the `definitions` field. Each definition describes exactly how a list can be executed.

> This operation returns static business metadata. There is no pagination and no edges/nodes pattern.

### Header

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| x-api-key | Primary or secondary subscription key of your app |

### GraphQL Query

```graphql
query {
  listCatalog(contextContains: "Invoice") {
    definitions {
      entityKey
      context
      filters
      orderBy
      supportsDateRange
    }
  }
}
```

### Example Response

```json
{
  "data": {
    "listCatalog": {
      "definitions": [
        {
          "entityKey": "salesInvoices",
          "context": "Sales Invoices",
          "filters": [
            "All invoices",
            "Validated invoices",
            "Draft",
            "Unpaid - upcoming",
            "Unpaid - overdue",
            "Partially paid - upcoming",
            "Partially paid - overdue",
            "Paid",
            "Credit notes",
            "Payment in advance",
            "National invoices",
            "Foreign invoices",
            "Search by customer id"
          ],
          "orderBy": [
            "Invoice date desc",
            "Invoice number"
          ],
          "supportsDateRange": true
        },
        {
          "entityKey": "salesInvoiceLines",
          "context": "Sales Invoice Lines",
          "filters": [
            "All invoice lines",
            "Validated invoices",
            "Credit note lines",
            "Standard invoice lines",
            "National invoice lines",
            "Foreign invoice lines",
            "Search by customer id",
            "Search by product id"
          ],
          "orderBy": [
            "Invoice date desc",
            "Invoice number",
            "Product",
            "Quantity desc"
          ],
          "supportsDateRange": true
        }
      ]
    }
  }
}
```

### listCatalog Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| contextContains | String | No | Filters list definitions by matching the value against `context` |

### listCatalog Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| definitions[] | ListDefinition | -- | List of available list catalog definitions |

### Definition Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| entityKey | String | -- | Technical identifier of the list. To reuse as-is as `entityKey` in `listExecute`. |
| context | String | -- | Human-readable business description of the list purpose. |
| filters[] | [String] | -- | Predefined business filters that can be reused as-is as `filterName` in `listExecute`. |
| orderBy[] | [String] | -- | Allowed sorting rules to reuse as-is as `orderByName` in `listExecute`. |
| supportsDateRange | Boolean | -- | Indicates whether the list supports date range filtering. Date parameters can only be provided in `listExecute` when this value is true. |

> **Execution contract:** All values returned by `listCatalog` define the strict execution contract for `listExecute`. Filters and sorting rules must be reused as-is. Date parameters are only allowed when `supportsDateRange` is true. Any value not explicitly exposed by the catalog is invalid for list execution.

---

## List Execute

### Description

The `listExecute` operation executes a business list strictly based on the definitions returned by `listCatalog`.

This operation does not require any knowledge of the underlying GraphQL queries or schema. It relies entirely on the metadata exposed by the catalog.

Most parameters provided to this operation must come from the values returned by `listCatalog`. This guarantees:

- Valid list definitions
- Consistent business filtering
- Controlled sorting rules
- Safe execution without invalid fields
- The ability to build lists dynamically without prior API knowledge

### Sample of Result

Thanks to the List API, this result is obtained with a single call to `listExecute`, using only predefined business parameters:

- A list definition (`entityKey = salesInvoiceLines`)
- A business filter (`filter = Unpaid invoice lines`)
- A sorting rule (`orderBy = Invoice date desc`)

The engine automatically handles:

- Validation of the list definition
- Validation of allowed filters
- Validation of allowed sorting rules
- Execution of the underlying business query
- Pagination and total count

The result returns a business-ready list, without requiring any knowledge of the underlying GraphQL schema.

| Invoice Number | Invoice Date | Customer | Product Code | Quantity | Total Net |
|----------------|-------------|----------|-------------|----------|-----------|
| SI-2026-0145 | 2026-03-12 | Opale | PROD-001 | 2 | 1,200.00 |
| SI-2026-0138 | 2026-03-10 | Rubis sur ongle | PROD-014 | 1 | 850.00 |
| SI-2026-0122 | 2026-03-05 | Bague's en or 13 | PROD-007 | 3 | 2,100.00 |
| SI-2026-0119 | 2026-03-04 | Carat S.a.r.l | PROD-003 | 5 | 3,750.00 |
| SI-2026-0115 | 2026-03-01 | Dupond INC | PROD-021 | 1 | 420.00 |

### Functionality

- Execute predefined business lists
- Apply business filters defined in the catalog
- Apply controlled sorting rules
- Execute business search filters when required
- Retrieve business entities or document lines
- Handle pagination and total count automatically
- Secure data access by design

### Important Principle

`listExecute` must always be used after calling `listCatalog`.

The catalog defines the exact allowed values for:

- `entityKey`
- `filterName`
- `orderByName`

Some filters may require an additional search value. If a value is not present in `listCatalog`, it is invalid for `listExecute`.

### Header

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId (deprecated) | Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### GraphQL Query

```graphql
query ($input: ListExecuteDtoInput!) {
  listExecute(input: $input) {
    meta {
      entityKey
      context
      filterName
      orderByName
      dateMin
      dateMax
      search
    }
    rows
    totalCount
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

### Example Variables (Standard Filter)

```json
{
  "input": {
    "entityKey": "salesInvoiceLines",
    "filterName": "National invoice lines",
    "orderByName": "Invoice date desc",
    "dateMin": "2026-01-01",
    "dateMax": "2026-03-31",
    "after": null
  }
}
```

### Example Response (Standard Filter)

```json
{
  "meta": {
    "entityKey": "salesInvoiceLines",
    "context": "Sales Invoice Lines",
    "policy": "salesInvoiceLines",
    "filterName": "National invoice lines",
    "orderByName": "Invoice date desc",
    "dateMin": "2026-01-01",
    "dateMax": "2026-03-31",
    "search": null
  },
  "edges": [
    {
      "node": {
        "invoiceNumber": "SI-2026-0145",
        "invoiceDate": "2026-03-12",
        "customerName": "Opale",
        "productCode": "PROD-001",
        "quantity": 2,
        "totalNet": 1200.0
      }
    },
    {
      "node": {
        "invoiceNumber": "SI-2026-0132",
        "invoiceDate": "2026-02-27",
        "customerName": "Azur Distribution",
        "productCode": "PROD-014",
        "quantity": 5,
        "totalNet": 3250.0
      }
    }
  ],
  "totalCount": 42,
  "pageInfo": {
    "hasNextPage": true,
    "endCursor": "OjE="
  }
}
```

### Example Variables (Search-Based Filter)

```json
{
  "input": {
    "entityKey": "customers",
    "filterName": "Search by company name",
    "search": "Opale",
    "orderByName": "Name asc",
    "after": null
  }
}
```

### Example Response (Search-Based Filter)

```json
{
  "meta": {
    "entityKey": "customers",
    "context": "Customers",
    "policy": "customers",
    "filterName": "Search by company name",
    "orderByName": "Name asc",
    "search": "Opale"
  },
  "edges": [
    {
      "node": {
        "code": "OPALE",
        "socialName": "Opale",
        "customerType": "B2B",
        "countryIsoCodeAlpha2": "FR"
      }
    }
  ],
  "totalCount": 1,
  "pageInfo": {
    "hasNextPage": false,
    "endCursor": null
  }
}
```

### listExecute Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| entityKey | String | Yes | List definition identifier. Must match an `entityKey` returned by `listCatalog`. |
| filterName | String | Yes | Business filter applied to the list. Must match exactly one filter name returned by `listCatalog`. Some filters are search-based and contain a `%%SEARCH%%` placeholder (e.g. "Search by company name", "Search by contact name"). |
| search | String | Conditional | Search value used by the selected business filter. Required when using a filter that expects a search value. Must not be provided when using filters that do not support search. |
| orderByName | String | Yes | Sorting rule applied to the list. Must match exactly one `orderBy` name returned by `listCatalog`. |
| dateMin | String | No | Start date of the business period (ISO 8601, e.g. `2024-01-01`). Only allowed when `supportsDateRange` is true in the catalog. Optional; if omitted, no lower bound is applied. |
| dateMax | String | No | End date of the business period (ISO 8601, e.g. `2024-12-31`). Must be >= dateMin. Only allowed when `supportsDateRange` is true in the catalog. Optional; if omitted, no upper bound is applied. |
| first | Int | No | Maximum number of items to return. Used for pagination (Relay specification). Default is 20, maximum is 500. |
| after | String | No | Cursor for pagination (Relay specification). |

### listExecute Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| meta | ListExecuteMeta | -- | Echo of the executed list parameters. Values may differ from input if certain parameters were inconsistent. The meta section always reflects the final parameters actually applied during execution. |
| rows | JSON | -- | List results following the Relay pagination model. The structure depends on the underlying list definition and associated GraphQL query. The List API does not alter the shape of the returned business objects. |
| totalCount | Int | -- | Total number of records matching the filter, independently of pagination. |
| pageInfo | ListExecutePageInfo | -- | Pagination metadata used to retrieve subsequent pages. |

### Meta Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| entityKey | String | -- | Executed list definition identifier |
| context | String | -- | Human-readable list context |
| filterName | String | -- | Business filter applied |
| orderByName | String | -- | Sorting rule applied |
| dateMin | String | -- | Effective start date used by the engine |
| dateMax | String | -- | Effective end date used by the engine |
| search | String | -- | Search value used to restrict results (if any) |

### pageInfo Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| hasNextPage | Boolean | -- | Indicates whether more results are available |
| endCursor | String | -- | Cursor to retrieve the next page |
