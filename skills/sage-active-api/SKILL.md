---
name: sage-active-api
description: >
  Complete reference for Sage Active Public API V2 (GraphQL). Use this skill whenever
  constructing GraphQL queries or mutations for Sage Active, working with Sage Active
  entities (customers, suppliers, employees, products, sales documents, purchase documents,
  accounting entries, accounting accounts), implementing OAuth2 authentication with
  Sage Active, or needing field definitions, business rules, legislation-specific rules
  (FR, ES, DE), pagination, filtering, or error handling for the Sage Active API.
  Trigger on any mention of "Sage Active", "sage active api", "sage graphql",
  "sage active query", or any Sage Active entity names like customers, salesInvoices,
  accountingEntries, etc.
---

# Sage Active Public API V2 — Skill Reference

A **GraphQL API** built with Hot Chocolate (.NET), available for three legislations: FR (France), ES (Spain), DE (Germany). PT (Portugal) support is being introduced — PT-specific fields are already shipping and `legislationCode` gains `PT` with the upcoming `organizations`/`organizationDetail` split (no public PT endpoint documented yet).

Official documentation: https://developer.sage.com/sageactive/
Postman collection: https://developer.sage.com/sageactive/files/Sage%20Active%20Public%20API%20V2.postman_collection.json

## Getting Started (New Project)

To bootstrap a new Sage Active integration:
1. **Subscribe** to Sage Active for your legislation (FR/ES/DE)
2. **Access Developer Center** from "Your Sage Active"
3. **Create an app** → get Client ID, Client Secret, Subscription Key
4. **Configure Solutions** tab (target markets: FR, ES, DE)
5. **Implement OAuth2** — see `references/00-endpoints-auth.md`
6. **First query**: `userProfile` (no X-OrganizationId needed), then `organizations` to get the X-OrganizationId
7. **Architecture**: Backend-only API access, secrets in env vars, cache static data

Read `references/00-endpoints-auth.md` for complete getting started guide with all URLs and auth flows.

## Endpoints

| Legislation | GraphQL URL |
|-------------|-------------|
| FR | `https://api.fr.active.sage.com/graphql` |
| ES | `https://api.es.active.sage.com/graphql` |
| DE | `https://api.de.active.sage.com/graphql` |

Auth (all): `https://sbcauth.sage.fr/connect/authorize` | Token: `.../connect/token`

## Required Headers

| Header | Required | Value |
|--------|----------|-------|
| `Authorization` | Always | `Bearer {access_token}` |
| `x-api-key` | Always | Subscription key from app registration |
| `X-OrganizationId` | Almost always | Organization UUID (not needed for: userProfile, organizations, localizedEnumValues, localizedErrorMessage) |

## Entity Quick Reference

| Entity | Query Name | Create Mutation | Update Mutation | Delete Mutation | Reference |
|--------|-----------|-----------------|-----------------|-----------------|-----------|
| **Organizations** | `organizations` | — | — | — | 15-reference-data.md |
| **Organization Detail** | `organizationDetail` (COMING SOON — organizations split) | — | — | — | 15-reference-data.md |
| **User Profile** | `userProfile` | — | — | — | 15-reference-data.md |
| **Users** | `users` | — | — | — | 15-reference-data.md |
| **Customers** | `customers` | `createCustomer` | `updateCustomer` | `deleteCustomer` | 02-customers.md |
| **Suppliers** | `suppliers` | `createSupplier` | `updateSupplier` | `deleteSupplier` | 03-suppliers.md |
| **Employees** | `employees` | `createEmployee` | `updateEmployee` | `deleteEmployee` | 04-employees.md |
| **Products** | `products` | `createProduct` | `updateProduct` | `deleteProduct` | 05-products.md |
| **Product Prices** | `productPriceService` (action) | — | — | — | 05-products.md |
| **Tariffs** | `tariffs` | — | — | — | 05-products.md |
| **Discounts** | `discounts` | — | — | — | 05-products.md |
| **Sales Quotes** | `salesQuotes` / `salesQuoteLines` | `createSalesQuote` | `updateSalesQuote` | `deleteSalesQuote` | 06-sales-quotes.md |
| **Sales Orders** | `salesOrders` / `salesOrderLines` | `createSalesOrder` | `updateSalesOrder` | `deleteSalesOrder` | 07-sales-orders.md |
| **Sales Delivery Notes** | `salesDeliveryNotes` / `salesDeliveryNoteLines` | `createSalesDeliveryNote` | `updateSalesDeliveryNote` | `deleteSalesDeliveryNote` | 08-sales-delivery-notes.md |
| **Sales Invoices** | `salesInvoices` / `salesInvoiceLines` | `createSalesInvoice` | `updateSalesInvoice` | `deleteSalesInvoice` | 09-sales-invoices.md |
| **Close Sales Invoice** | — | `closeSalesInvoice` (action) | — | — | 09-sales-invoices.md |
| **Post Sales Invoice** | — | `postSalesInvoice` (action) | — | — | 09-sales-invoices.md |
| **Credit Note** | — | `generateCreditNote` (action) | — | — | 10-sales-actions.md |
| **Document PDF Preview** | `documentPdfPreview` (action) | — | — | — | 10-sales-actions.md |
| **Document PDF Email** | — | `sendDocumentPdfEmail` (action) | — | — | 10-sales-actions.md |
| **Sales Invoice Open Items** | `salesInvoiceOpenItems` | — | — | — | 10-sales-actions.md |
| **Sales Settlement** | — | `createSalesOpenItemSettlement` (action) | — | — | 10-sales-actions.md |
| **Related Sales Docs** | `relatedSalesDocuments` | — | — | — | 10-sales-actions.md |
| **Sales KPIs** | `salesKpis` (action) | — | — | — | 10-sales-actions.md |
| **Purchase Invoices** | `purchaseInvoices` | `createPurchaseInvoice` | `updatePurchaseInvoice` | `deletePurchaseInvoice` | 11-purchase-invoices.md |
| **Post Purchase Invoice** | — | `postPurchaseInvoice` (action) | — | — | 11-purchase-invoices.md |
| **Purchase Open Items** | `purchaseInvoiceOpenItems` | — | — | — | 11-purchase-invoices.md |
| **Purchase Settlement** | — | `createPurchaseOpenItemSettlement` | — | — | 11-purchase-invoices.md |
| **Accounting Accounts** | `accountingAccounts` | `createAccountingAccount` | `updateAccountingAccount` | `deleteAccountingAccount` | 12-accounting-accounts.md |
| **Accounting Entries** | `accountingEntries` / `accountingEntryLines` | `createAccountingEntryUsingCodes` / `createAccountingEntryUsingIds` | `updateAccountingEntry` | `deleteAccountingEntry` | 13-accounting-entries.md |
| **Trial Balance** | `accountingTrialBalance` (action) | — | — | — | 14-accounting-reports.md |
| **Third Party Balance** | `accountingThirdPartyBalance` (action) | — | — | — | 14-accounting-reports.md |
| **Balance Sheet** | `accountingBalanceSheet` (action) | — | — | — | 14-accounting-reports.md |
| **Profit & Loss** | `accountingProfitAndLoss` (action) | — | — | — | 14-accounting-reports.md |
| **Accounting KPIs** | `accountingKpis` (action) | — | — | — | 14-accounting-reports.md |
| **Countries** | `countries` | — | — | — | 15-reference-data.md |
| **Currencies** | `currencies` | — | — | — | 15-reference-data.md |
| **Taxes** | `taxes` | — | — | — | 15-reference-data.md |
| **Tax Groups** | `taxGroups` | — | — | — | 15-reference-data.md |
| **Journal Types** | `journalTypes` | — | — | — | 15-reference-data.md |
| **Accounting Exercises** | `accountingExercises` | — | — | — | 15-reference-data.md |
| **Bank Accounts** | `bankAccounts` | — | — | — | 15-reference-data.md |
| **Bank Movements** | `bankMovements` | — | — | — | 15-reference-data.md |
| **Banking Rules** | `bankingRules` | — | — | — | 15-reference-data.md |
| **Reconcile Bank Movement** | — | `reconcileBankMovement` (action) | — | — | 15-reference-data.md |
| **Unreconcile Bank Movement** | — | `unReconcileBankMovement` (action) | — | — | 15-reference-data.md |
| **Payment Methods** | `paymentMethods` (legacy alias `paymentMeans`) | — | — | — | 15-reference-data.md |
| **Invoice Types** | `invoiceTypes` | — | — | — | 15-reference-data.md |
| **Operational Number Preset Texts** | `operationalNumberPresetTexts` | — | — | — | 15-reference-data.md |
| **Payment Terms** | `paymentTerms` | — | — | — | 15-reference-data.md |
| **Dimensions** | `dimensions` | — | — | — | 15-reference-data.md |
| **Org Sales Setup** | `organizationSalesSetupByOrgId` | — | — | — | 15-reference-data.md |
| **Org Accounting Setup** | `organizationAccountingSetupByOrgId` | — | — | — | 15-reference-data.md |
| **Org Sales Setup Docs** | `organizationSalesSetupDocsCustomizationByOrgId` | — | — | — | 15-reference-data.md |
| **Org IRPF Setup (ES)** | `organizationIrpfSetupByOrgId` | — | — | — | 15-reference-data.md |
| **Org Global Setup (FR)** | `organizationGlobalSetupByOrgId` | — | — | — | 15-reference-data.md |
| **File Upload** | — | `uploadFileToEntity` (action) | — | — | 16-file-management.md |
| **File List** | `files` | — | — | — | 16-file-management.md |
| **File Download** | `fileDownloadById` (action) | — | — | — | 16-file-management.md |
| **Aggregation Catalog** | `aggregationCatalog` (action) | — | — | — | 17-aggregations-lists.md |
| **Aggregation Execute** | `aggregationExecute` (action) | — | — | — | 17-aggregations-lists.md |
| **List Catalog** | `listCatalog` (action) | — | — | — | 17-aggregations-lists.md |
| **List Execute** | `listExecute` (action) | — | — | — | 17-aggregations-lists.md |

## Core Patterns (Quick Reference)

### Pagination (max 500 records, default 20)
```graphql
entities(first: 100, after: $cursor) { nodes { ... } pageInfo { endCursor hasNextPage } totalCount }
```

### Filtering
```graphql
where: { socialName: { contains: "acme" }, status: { eq: "ENABLED" } }
where: { or: [{ socialName: { contains: "x" } }, { code: { contains: "x" } }] }
where: { documentDate: { gte: "2024-01-01", lte: "2024-12-31" } }
```
Operators: `eq`, `neq`, `contains`, `startsWith`, `endsWith`, `gt`, `gte`, `lt`, `lte`, `in`, `nin`, `and`, `or`

### Sorting
```graphql
order: [{ documentDate: DESC }, { totalGross: DESC }]
```

### Updates (patch-style)
Only send changed fields + `id`. For sub-resources use `requestedAction` (CREATE/MODIFY/DELETE) or `replaceAll`. See `references/19-update-patterns.md`.

### Error Handling
HTTP 200 with `errors[]` for business errors. HTTP 401 for auth. HTTP 429 for rate limit (3000 req/min).

## Rate Limits & Best Practices

- **3000 requests/app/minute** — Handle 429 with `Retry-After`
- **Max 10 parallel mutations** (sequential numbers)
- **maxFieldCost: 650000** — Request only needed fields
- **Cache static data** (countries, currencies, taxes, journal types, UoM)
- Access token: **8 hours**. Use refresh token for renewal.

## Reference Files Index

| File | Content |
|------|---------|
| `00-endpoints-auth.md` | Endpoints, OAuth2 (3 flows), getting started, best practices |
| `01-graphql-patterns.md` | Pagination, filtering, sorting, DataLoaders, errors, complexity |
| `02-customers.md` | Customer CRUD — ALL fields, sub-objects, legislation rules |
| `03-suppliers.md` | Supplier CRUD — ALL fields, differences from customers |
| `04-employees.md` | Employee CRUD — ALL fields |
| `05-products.md` | Products, tariffs, discounts, product price service |
| `06-sales-quotes.md` | Sales quotes CRUD — ALL fields, lines |
| `07-sales-orders.md` | Sales orders CRUD — ALL fields, lines |
| `08-sales-delivery-notes.md` | Delivery notes CRUD — ALL fields, lines |
| `09-sales-invoices.md` | Sales invoices CRUD, close, post, advance, online payment |
| `10-sales-actions.md` | Credit notes, PDF, email, open items, settlements, KPIs |
| `11-purchase-invoices.md` | Purchase invoices CRUD, post, settlements |
| `12-accounting-accounts.md` | Chart of accounts, plan master |
| `13-accounting-entries.md` | Accounting entries (by codes & IDs), sessions |
| `14-accounting-reports.md` | Trial balance, balance sheet, P&L, third party balance, KPIs |
| `15-reference-data.md` | Organizations (+ organizationDetail split), users, countries, currencies, taxes, invoice types, banks, bank movements, banking rules, reconcile/unreconcile actions, payment methods, org setup queries (sales/accounting/docs/IRPF/global) |
| `16-file-management.md` | File upload/download/export, AP automation |
| `17-aggregations-lists.md` | Aggregation & List catalog/execute |
| `18-legislation-rules.md` | Consolidated FR/ES/DE rules |
| `19-update-patterns.md` | requestedAction, replaceAll, nested update examples |

## Source Documentation URLs

| Section | Base URL |
|---------|----------|
| Main Portal | https://developer.sage.com/sageactive/ |
| API Resources | https://developer.sage.com/sageactive/resources/{entity} |
| Key Concepts | https://developer.sage.com/sageactive/concepts/{topic} |
| Tutorials | https://developer.sage.com/sageactive/guides/{topic} |
| FAQ | https://developer.sage.com/sageactive/faq/ |
| Postman Collection | https://developer.sage.com/sageactive/files/Sage%20Active%20Public%20API%20V2.postman_collection.json |
| Schema Viewer | https://developer.sage.com/sageactive/resources/schema |

For any information not covered in these references, consult the live documentation at the URLs above.

## Skill Versioning

This skill is updated to track Sage's monthly releases. See [`CHANGELOG.md`](CHANGELOG.md) for what changed in each release and the **Open items** checklist of features still in COMING SOON state.

**Current Sage release tracked:** 2026-06 ("organizations split (upcoming), setup APIs, and new fields").

> ⚠️ Heads-up for integrators: the next Sage release splits `organizations` (selection fields only) from the new `organizationDetail` query (full configuration). See the Open items section of `CHANGELOG.md` and the migration guide at <https://developer.sage.com/sageactive/resources/organizations_new>.

When Sage ships a new release, follow the *"How to update this skill"* runbook at the top of `CHANGELOG.md`.
