# Changelog — Sage Active Public API V2 Skill

This changelog tracks how the **skill** stays in sync with Sage Active Public API V2 releases.
The canonical Sage release notes live at <https://developer.sage.com/sageactive/?link=last>.

## Conventions

### Inline COMING SOON marker

Anywhere a field, mutation or query is documented in `references/` but is not yet live in production, mark it like this:

| Form | Where to use |
|------|-------------|
| `🚧 COMING SOON (announced YYYY-MM)` | Inline inside a table cell — keep tables narrow |
| `> 🚧 **COMING SOON** — announced YYYY-MM — [source](url)` | Standalone callout above a section |

`announced YYYY-MM` is the **Sage release** the feature was announced in. When a feature ships, move it from the *Open items* checklist below to the *Promoted* section of that release's entry.

### How to update this skill when Sage ships a new release

1. Read the latest release notes at <https://developer.sage.com/sageactive/?link=last>.
2. For each item under **Public API Improvements** add it inline under the relevant entity in `references/`. Mark COMING SOON items with the convention above.
3. Walk the **Open items** checklist below — anything Sage now lists as live should be:
   - Removed from *Open items*
   - Added to the **Promoted from COMING SOON → live** section of the new release entry
   - Have its inline `🚧` marker removed in `references/`
4. Add a new entry at the top of this file (`## [YYYY-MM]`) summarising what changed.
5. Bump the version line at the bottom of the project `README.md` (*"API Version: V2 (… release)"*).

---

## [2026-06] — organizations split (upcoming), setup APIs, and new fields

Source: <https://developer.sage.com/sageactive/?link=last>
Title: *"organizations split (upcoming), setup APIs, and new fields"*

### ⚠️ Upcoming breaking change announced

- **`organizations` / `organizationDetail` split** — in the next Sage release, `organizations` (LIST + READ by id) returns only selection fields (`id`, `creationDate`, `modificationDate`, `onboardingCompleted`, `onboardingDateCompleted`, `legislationCode`, `socialName`). Full configuration moves to a new `organizationDetail` READ query (resolved via `X-OrganizationId`). Fields that move will still be queryable on `organizations` but return `null`. Migration guide: <https://developer.sage.com/sageactive/resources/organizations_new>. Documented with a COMING SOON callout in [15-reference-data.md](references/15-reference-data.md#organizations).
- The new-method page documents `legislationCode` as `FR, ES, DE or PT` — **Portuguese legislation** support is being introduced (PT-specific fields already shipping, see below).

### Added (live)

- **`organizationSalesSetupDocsCustomizationByOrgId`** query — sales document customization settings (layout, style, colours, items tables, footer, copies). See [15-reference-data.md](references/15-reference-data.md#organization-sales-setup-docs-document-customization).
- **`organizationIrpfSetupByOrgId`** query (ES only) — IRPF withholding setup (name, percentage). See [15-reference-data.md](references/15-reference-data.md#organization-irpf-setup-es-only).
- **`organizationGlobalSetupByOrgId`** query (FR only) — the three mandatory legal mentions (BT-22 / AAB, PMD, PMT). See [15-reference-data.md](references/15-reference-data.md#organization-global-setup-fr-only).
- **Simplified Invoice dedicated docs page** — <https://developer.sage.com/sageactive/resources/simplifiedinvoices>; rules consolidated in [18-legislation-rules.md](references/18-legislation-rules.md).
- `GenerateCreditNoteGLDtoInput.outputDocumentDate` — output document date for partial credit notes.
- `purchaseInvoices/vatLines.transactionCategory` / `.transactionCategoryId` — now exposed on vatLines.
- `userProfile.firstName` / `.lastName` (read-only).
- `users.auth0UserId` / `.firstName` / `.lastName` (read-only); `users.fullName` is computed (order/filter on firstName/lastName instead).
- `createAccountingEntryUsingCodes` / `createAccountingEntryUsingIds` accountingEntryLines: `isGroupingInvoices`, `startInvoiceNumber`, `finishInvoiceNumber` (optional inputs for grouped simplified invoices).
- `organizations.useWithholdingTaxTreatmentId` — tax treatment used for sales withholding tax.
- Sales document **lines** (quotes, orders, delivery notes, invoices): `tax`, `taxId`, `taxTreatmentId` (read-only).
- `salesInvoices.salesInvoiceTemplateId` (read-only) — non-null means the invoice is a Recurring invoice.
- **Portuguese legislation fields**: `accountingAccounts.taxonomy`; `organizations.useThirdPartyBilling` / `.thirdPartyBillingName` / `.thirdPartyBillingVatNumber`; `organizationAccountingSetup.defaultFinancialDiscountAccountId`; `organizationSalesSetup.salesReceiptDefaultPresetTextId`; `operationalNumberPresetTexts.operationalNumberSeriesType` (NOT_SPECIFIED, NORMAL, RECOVERY_MANUALLY, RECOVERY_IMPORTED).

### Promoted from COMING SOON → live

- `operationalNumberPresetText` / `operationalNumberPresetTextId` on sales documents (quotes, orders, delivery notes, invoices) — verified live (marked NEW, no COMING SOON) on the resource pages.
- `User.lastName` and `User.fullName` — live on `users` and `userProfile`.

### Notes

- Postman collection refreshed for June: <https://developer.sage.com/sageactive/files/Sage%20Active%20Public%20API%20V2.postman_collection.json>.
- The portal shows a persistent warning banner about the upcoming `organizations` change.

---

## [2026-05] — Unreconcile bank movements, Spanish simplified invoice fields

Source: <https://developer.sage.com/sageactive/?link=last>
Title: *"Unreconcile bank movements, Spanish simplified invoice fields"*

### Added (live)

- New **`invoiceTypes`** query — invoice type classifications: `description`, `isAmendInvoice`, `registerType` (BOTH/RECEIVED/ISSUED), `siiCode` (Spanish SII), `invoiceTypesCode`. See [15-reference-data.md](references/15-reference-data.md#invoice-types).
- `accountingEntries/accountingEntryLines/accountingEntryInvoice` (read-only): `isGroupingInvoices`, `startInvoiceNumber`, `finishInvoiceNumber`, `invoiceType`, `invoiceTypeId` — grouped simplified invoices. See [13-accounting-entries.md](references/13-accounting-entries.md).
- `DocumentPdfEmailGLDtoInput.emailBcc` — BCC recipient email addresses.
- `GenerateCreditNoteGLDtoInput.operationalNumberPresetText` / `.operationalNumberPresetTextId` — preset text for generated credit notes.
- `organizationAccountingSetupByOrgId.defaultFixedAssetJournalTypeId`.

### Promoted from COMING SOON → live

- **`unReconcileBankMovement`** mutation — undo reconciliation via `bankTransactionId` only. (Release notes write it `unreconcileBankMovement`, but the actual mutation name is `unReconcileBankMovement` — capital R — per the resource page.)
- **Spain — simplified invoices**:
  - `Customer.nonIdentifiedCustomer` (requires `customerType = INDIVIDUAL`)
  - `Customer.printNameOnPdf`
  - `SalesInvoice.printNameOnPdf`
  - `OrganizationSalesSetup.askGenerateNonIdentifiedSalesInvoicesByDefault` (read-only)
  - `OrganizationSalesSetup.nonIdentifiedSalesInvoiceMaxAmount` (read-only)

---

## [2026-04] — Bank reconciliation refresh + Sales Invoices

Source: <https://developer.sage.com/sageactive/?link=last>
Title: *"Bank reconciliation documentation refresh and new Sales Invoices capabilities"*

### Added (live)

- **`bankMovements`** resource — full schema (header + `linkedAccountingEntries[]` + `proposalItems[]` + `reconciledItems[]`). See [15-reference-data.md](references/15-reference-data.md#bank-movements).
- **`bankingRules`** resource — full schema (drives matching/posting of bank movements). See [15-reference-data.md](references/15-reference-data.md#banking-rules).
- **`reconcileBankMovement`** mutation — links a bank movement to one or more accounting entries, optionally tagged with a banking rule. See [15-reference-data.md](references/15-reference-data.md#reconcile-bank-movement).
- Bank reconciliation documentation pages restructured by Sage (Banks / Bank Accounts / Payment methods / Bank movements / Reconcile / Banking rules).

### Promoted from COMING SOON → live

- (none — first release tracked under this changelog system).

### New COMING SOON (revisit next release)

- **`unReconcileBankMovement`** mutation — undoes reconciliation for a movement (`bankTransactionId`). See [15-reference-data.md](references/15-reference-data.md#unreconcile-bank-movement).
- **Spain — simplified invoices** (3 entities):
  - `Customer.nonIdentifiedCustomer` (Boolean) — flag customer as simplified-invoice target
  - `Customer.printNameOnPdf` (Boolean) — default for printing customer name on simplified invoices
  - `SalesInvoice.printNameOnPdf` (Boolean) — print customer name on this document's simplified invoice PDF
  - `OrganizationSalesSetup.askGenerateNonIdentifiedSalesInvoicesByDefault` (Boolean, read-only) — org-wide default
  - `OrganizationSalesSetup.nonIdentifiedSalesInvoiceMaxAmount` (Decimal, read-only) — warning threshold

### Notes

- `paymentMeans` resource is now exposed by Sage as `paymentMethods`. Both names appear in the official docs index. The skill entity quick reference now uses `paymentMethods`.
- Postman collection refreshed for April release: <https://developer.sage.com/sageactive/files/Sage%20Active%20Public%20API%20V2.postman_collection.json>.

---

## Open items (pending Sage promotion to live)

Checklist of all `🚧 COMING SOON` markers currently in `references/`. When Sage promotes any of these, move it to a release entry above and clear its inline marker.

- [ ] **`organizations` / `organizationDetail` split** — announced 2026-06 — [migration guide](https://developer.sage.com/sageactive/resources/organizations_new). When live: `organizations` returns only selection fields (the rest go `null`); full config moves to the new `organizationDetail` query; `legislationCode` gains `PT`. On promotion, restructure the Organizations section of [15-reference-data.md](references/15-reference-data.md) into the two-operation model and re-check whether a PT GraphQL endpoint is published (SKILL.md endpoints table + legislation claims in 00/18).

---

## How to verify this skill is up to date with the latest Sage release

```bash
# Quick smoke check — version line in README and CHANGELOG top entry should match
grep -n "API Version" README.md
head -n 5 skills/sage-active-api/CHANGELOG.md | grep -E "^## \[2026-"

# All open COMING SOON markers should match the Open items checklist
grep -rn "COMING SOON\|🚧" skills/sage-active-api/references/ | wc -l
```
