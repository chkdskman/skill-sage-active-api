# Changelog — Sage Active Public API V2 Skill

This changelog tracks how the **skill** stays in sync with Sage Active Public API V2 releases.
The canonical Sage release notes live at <https://developer.sage.com/sageactive/?link=last>.

## 2026-09-17 — Commercial transformations and cleanup

- Verified native Quote → Order → Delivery Note → Invoice origins, related links, partial order quantities and duplicate conversion rejection against the ES API in an authorized test company.
- Documented that deleting the target invoice did not reopen its closed delivery note, preventing dependent fixture cleanup. No undocumented reopening operation is suggested.
- Marked delivery-note status explicitly read-only, consistent with the official resource documentation.
- Verified catalog selections and pagination for taxGroups, taxes, unitOfMeasurements, paymentMeans, paymentMethods, paymentTerms and accountingExercises. These are live handler/API checks, not UI or LLM end-to-end coverage.

## 2026-09-16 — Assistant capability campaign

- Corrected nested purchase invoice OpenItem fields: purchaseInvoiceId is not selectable there (live ES HTTP 400; corrected lookup succeeds). Kept the separate purchaseInvoiceOpenItems contract unchanged.
- Documented live ES rejection of userProfile.userId and successful selection of id.
- Removed the obsolete X-TenantId requirement from the organization selection prose, aligning it with the existing deprecation note.
- Read-only validation: accountingBalanceSheet and accountingProfitAndLoss examples succeeded on ES for 2026-01-01 through 2026-09-16. No accounting or business mutations were executed for this verification.

## 2026-09-16 — Organization detail response correction

- Corrected organizationDetail examples and response shape: OrganizationDetailConnection with nodes, not a direct object.
- Verified by a read-only ES production query returning HTTP 200 with organizationDetail.nodes and currency; direct field selection returned HTTP 400.

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

## [2026-09 V1] — e-invoicing for France, fixed assets, and new API fields

Source: <https://developer.sage.com/sageactive/?link=last>
Title: *"e-invoicing for France and new API fields"*

### Added (live)

- **🇫🇷 Electronic invoicing (`eInvoice`)** — new dedicated docs page and API surface, FR legislation only. Consolidated in the new [20-einvoice-fr.md](references/20-einvoice-fr.md).
  - **`organizationEInvoiceSetupByOrgId`** query — registration/connection with the Sage *Plateforme Agréée* (PA): `status` (`Accepted`/`InProgress`/`Failed`), mandate number, company info, authorized user, effective dates, e-reporting toggles, subscription ids, `errorCode`, terms acceptance.
  - **`retrySalesInvoiceElectronicSubmission`** mutation — resubmit a sales invoice to the PA after a failed submission (`salesInvoiceId` → `id`). Also cross-referenced in [10-sales-actions.md](references/10-sales-actions.md).
  - `customers.eInvoicingAddressType` / `.eInvoicingAddress` — routing directory identifier (`fr.siren`, `fr.siren_suffix`, `fr.sirensiret`, `fr.sirensiret_routing`). FR + `customerType = BUSINESS`. See [02-customers.md](references/02-customers.md).
  - `salesInvoices`: `eInvoiceId`, `eInvoicingAddressTypeCustomer`, `eInvoicingAddressCustomer`, `eInvoicingStatus`, `regulatoryBillingFramework` (`B1`/`S1`/`M1`/`B4`/`M4`/`S4`), `shouldSendViaElectronicInvoicing`, `eInvoice[]` (id, creationDate, status, rejectedReason, traceParent, salesInvoiceId). See [09-sales-invoices.md](references/09-sales-invoices.md).
  - `purchaseInvoices`: `eInvoiceId`, `isEInvoice`, `isEInvoiceCreditNote`, `eInvoicingStatus` + new `status` values `AwaitingDecision`, `Refused`, `PartiallyAccepted`, `Accepted`. See [11-purchase-invoices.md](references/11-purchase-invoices.md).
  - `postSalesInvoice` now auto-submits to the PA when `shouldSendViaElectronicInvoicing = true` and the routing/framework fields are filled.
- **`fixedAssets`** resource — full CRUD (`createFixedAsset` / `updateFixedAsset` / `deleteFixedAsset` / `fixedAssets`): code, name, description, purchasedDate, purchasePrice, acquisitionType (`NEW_ASSET`/`EXISTING_ASSET`), currentValue, accumulatedDepreciation, category and four accounting accounts. See [15-reference-data.md](references/15-reference-data.md#fixed-assets).
- **`fixedAssetCategories`** query — tangible/intangible categories mapped to ledger, depreciation, depreciation expense, current value and losses accounts. See [15-reference-data.md](references/15-reference-data.md#fixed-asset-categories).
- Sales document **lines** (quotes, orders, delivery notes, invoices): `applyEquivalenceSurcharge` (ES).
- `accountingEntries` (corrective invoices, ES): `originalInvoiceDate`, `originalInvoiceNumber`.
- `organizationSalesSetupDocsCustomization.salesReceiptsExportColor` — export colour for receipts (HEX).

### New COMING SOON (revisit next release)

- **`purchaseInvoices.purchaseType`** (`NONE` / `NORMAL` / `SIMPLIFIED`) — `NORMAL` required when the buyer needs VAT deduction; `SIMPLIFIED` for amounts up to €150 incl. VAT.
- **`purchaseInvoices.pageCount`** (Decimal) — number of pages of the invoice document.

### Notes

- New Sage docs pages picked up in this sync: 📝eInvoice (🇫🇷), 📝Cash VAT (🇫🇷🇩🇪), 📝Equivalence Surcharge (🇪🇸), 📝Withholding Tax (🇪🇸), 🌍Legislation rules, 📝Breaking changes. The last two are now linked from [18-legislation-rules.md](references/18-legislation-rules.md) and `SKILL.md`.
- **Portugal shares the ES Public API.** Sage documents three environments — FR, **ES/PT**, DE — so a PT organization is reached through `https://api.es.active.sage.com/graphql` and identified by `legislationCode = PT`. Corrected in [00-endpoints-auth.md](references/00-endpoints-auth.md) and `SKILL.md` (previously "no public PT endpoint documented").
- Postman collection refreshed for September: <https://developer.sage.com/sageactive/files/Sage%20Active%20Public%20API%20V2.postman_collection.json>. Filter tips — `🆕` for new items, `✏️` for changed items, `⚙️` for actions.

---

## [2026-07] — organizations split (LIVE), Cash VAT flag, simplified invoice preset

Source: <https://developer.sage.com/sageactive/?link=last>
Title: *"organizations split, setup APIs, and new fields"*

### ⚠️ Breaking change — now effective

- **`organizations` / `organizationDetail` split is live.** `organizations` (LIST + READ by id) returns only selection fields: `id`, `creationDate`, `modificationDate`, `status`, `onboardingCompleted`, `onboardingDateCompleted`, `legislationCode`, `socialName`. The full configuration moved to `organizationDetail` (resolved via `X-OrganizationId`). Fields that moved are still queryable on `organizations` but return `null`. [15-reference-data.md](references/15-reference-data.md#organizations) restructured into the two-operation model, with a two-step query example.
- **`organizations.status`** — new lifecycle enum (`UNDEFINED`, `BLOCKED`, `CANCELLED`, `EXPIRED`, `NO_LICENSE`, `PENDING`, `READY`, `RESET`, `TRANSFERRING`). A usable organization id is returned **only** when `status = READY` **and** `onboardingCompleted = true`; otherwise the API returns an empty GUID.
- Removed from the schema in 2026-06 (no compatibility break, harmless if still requested): `organizations.allowBlankIdentificationNumbers`, `organizations.useCustomerCodes`, `organizationSalesSetup.allowPostingSalesInvoice`, `organizationSalesSetup.allowPostingPurchaseInvoice`, and `contactEmail`/`contactName`/`contactPhone` on sales delivery note / invoice / order create+update inputs. Dropped from the reference tables where applicable.

### Added (live)

- Sales documents (quotes, orders, delivery notes, invoices): **`hasCashVat`** (read-only) — document subject to Cash VAT. New consolidated section in [18-legislation-rules.md](references/18-legislation-rules.md) covering the org → supplier → document → accounting-entry chain (`vatCriterion`, `hasCashVat`, `isCashVat`). Source: <https://developer.sage.com/sageactive/resources/cashvat>
- `organizationSalesSetup.nonIdentifiedSalesInvoiceDefaultPresetTextId` (ES, read-only) — default operational number preset text for simplified invoices.

### Promoted from COMING SOON → live

- `organizationDetail` query — was announced as upcoming in the previous entry, now live.

---

## [2026-06] — Organization Sales Setup Docs, IRPF Setup, Global Setup

Source: <https://developer.sage.com/sageactive/?link=last>
Title: *"Organization Sales Setup Docs, Organization IRPF Setup, Organization Global Setup"* (the `organizations` split was announced here as upcoming and shipped in 2026-07)

### ⚠️ Upcoming breaking change announced

- **`organizations` / `organizationDetail` split** — in the next Sage release, `organizations` (LIST + READ by id) returns only selection fields (`id`, `creationDate`, `modificationDate`, `onboardingCompleted`, `onboardingDateCompleted`, `legislationCode`, `socialName`). Full configuration moves to a new `organizationDetail` READ query (resolved via `X-OrganizationId`). Fields that move will still be queryable on `organizations` but return `null`. Migration guide: <https://developer.sage.com/sageactive/resources/organizations_new>. **Shipped in the 2026-07 release** — see that entry.
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

- [ ] **`purchaseInvoices.purchaseType`** (`NONE` / `NORMAL` / `SIMPLIFIED`) — announced 2026-09 — [purchase invoices](https://developer.sage.com/sageactive/resources/purchaseinvoices). On promotion, drop the marker in [11-purchase-invoices.md](references/11-purchase-invoices.md) and check whether it becomes an input on create/update.
- [ ] **`purchaseInvoices.pageCount`** (Decimal) — announced 2026-09 — number of pages of the invoice document. Same file.

## How to verify this skill is up to date with the latest Sage release

```bash
# Quick smoke check — version line in README and CHANGELOG top entry should match
grep -n "API Version" README.md
head -n 5 skills/sage-active-api/CHANGELOG.md | grep -E "^## \[2026-"

# All open COMING SOON markers should match the Open items checklist
grep -rn "COMING SOON\|🚧" skills/sage-active-api/references/ | wc -l
```
