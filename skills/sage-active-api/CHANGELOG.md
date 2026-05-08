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

- [ ] `unReconcileBankMovement` mutation — announced 2026-04 — [source](https://developer.sage.com/sageactive/resources/unreconcilebankmovement)
- [ ] `Customer.nonIdentifiedCustomer` (ES simplified invoice) — announced 2026-04 — [source](https://developer.sage.com/sageactive/resources/customers)
- [ ] `Customer.printNameOnPdf` (ES simplified invoice) — announced 2026-04 — [source](https://developer.sage.com/sageactive/resources/customers)
- [ ] `SalesInvoice.printNameOnPdf` (ES simplified invoice) — announced 2026-04 — [source](https://developer.sage.com/sageactive/resources/salesinvoices)
- [ ] `OrganizationSalesSetup.askGenerateNonIdentifiedSalesInvoicesByDefault` (ES) — announced 2026-04 — [source](https://developer.sage.com/sageactive/resources/organizationsalessetup)
- [ ] `OrganizationSalesSetup.nonIdentifiedSalesInvoiceMaxAmount` (ES) — announced 2026-04 — [source](https://developer.sage.com/sageactive/resources/organizationsalessetup)
- [ ] `operationalNumberPresetText` / `operationalNumberPresetTextId` on sales documents (quotes, orders, delivery notes, invoices) — announced ≤ 2026-03 — [source](https://developer.sage.com/sageactive/resources/operationalnumberpresettexts)
- [ ] `User.lastName` — announced ≤ 2026-03 — [source](https://developer.sage.com/sageactive/resources/users_usermanagement)
- [ ] `User.fullName` — announced ≤ 2026-03 — [source](https://developer.sage.com/sageactive/resources/users_usermanagement)

---

## How to verify this skill is up to date with the latest Sage release

```bash
# Quick smoke check — version line in README and CHANGELOG top entry should match
grep -n "API Version" README.md
head -n 5 skills/sage-active-api/CHANGELOG.md | grep -E "^## \[2026-"

# All open COMING SOON markers should match the Open items checklist
grep -rn "COMING SOON\|🚧" skills/sage-active-api/references/ | wc -l
```
