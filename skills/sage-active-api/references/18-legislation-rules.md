# Legislation-Specific Rules — FR, ES, DE (Consolidated)

> **Source:** Consolidated from all entity references in this skill.
> Sage Active supports three legislations: **FR** (France), **ES** (Spain), **DE** (Germany).
> The legislation is determined by `organizations.legislationCode`.

---

## 1. Customers & Suppliers — Document Identification

### Document Types by Legislation & Country

| Scenario | FR | ES | DE |
|----------|----|----|-----|
| **Domestic customer/supplier** | 01 SIRET | 01 NIF/DNI | 01 Steur-IdN |
| **VIES (intra-EU)** | 02 TVA Intracommunautaire | 02 NIF/IVA | 02 USt-IdNr |
| **Non-VIES (extra-EU)** | 06 Autre document justificatif | 06 Otro documento probatorio | 06 Another supporting document |

> Northern Ireland uses `countryIsoCodeAlpha2 = "XI"` (not "GB") for VIES purposes.

### Mandatory Fields — Customers

| Field | FR | ES | DE |
|-------|----|----|-----|
| `socialName` | Mandatory | Mandatory | Mandatory |
| `documentId` | Optional (SIRET 14 / SIREN 9 chars) | Optional (NIF/DNI/NIE) | Optional (Steuer-IdNr 11 chars) |
| `vatNumber` (domestic) | Recommended | Recommended | Recommended |
| `vatNumber` (VIES) | Mandatory | Mandatory | Mandatory |
| `vatNumber` (non-VIES) | Not used | Not used | Not used |
| `code` | Optional (auto-numbering) | Optional (auto-numbering) | **Mandatory** (auto, numeric 10000-69999) |
| `defaultAccountingAccountId` | Optional | Optional | **Mandatory** (collective account) |

### Mandatory Fields — Suppliers

| Field | FR | ES | DE |
|-------|----|----|-----|
| `documentId` | Optional (SIRET 14 / SIREN 9) | Optional (NIF/DNI/NIE) | Optional (Steuer-IdNr 11 chars) |
| `vatNumber` (VIES) | Mandatory | Mandatory | Mandatory |
| `code` | Optional (auto-numbering) | Optional (auto-numbering) | **Mandatory** (auto, numeric 70000-99999) |
| `defaultAccountingAccountId` | Optional | Optional | **Mandatory** (collective account) |

### Supplier-Only Fields by Legislation

| Field | Legislation | Description |
|-------|-------------|-------------|
| `vatCriterion` | FR only | Enable Cash VAT for supplier |
| `personalIncomeTax` | ES only | Whether personal income tax (IRPF) applies |
| `taxTreatmentId` | ES only | Tax treatment reference for purchases |
| `transactionCategoryId` | All | Auto-determines default accounting & tax treatment |

### Customer-Only Fields by Legislation

| Field | Legislation | Description |
|-------|-------------|-------------|
| `hasEquivalenceSurcharge` | ES only | Equivalence surcharge regime (retail VAT simplification) |

---

## 2. Employees

| Rule | FR | ES | DE |
|------|----|----|-----|
| **Availability** | Full CRUD | Full CRUD | **NOT available** |
| `code` range | Auto-numbering | Auto-numbering | N/A |
| Address limit | 1 only | 1 only | N/A |

- No legislation-specific identification fields (documentId, vatNumber) for employees.
- If connected to payroll, employees may be read-only (replicated from payroll system).

---

## 3. Sales Documents (Quotes, Orders, Delivery Notes, Invoices)

### Header Mandatory Fields

| Field | FR | ES | DE |
|-------|----|----|-----|
| `socialName` | **Mandatory** | Not mandatory | Not mandatory |
| `vatNumber` (VIES customers) | **Mandatory** | Not mandatory | Not mandatory |
| `documentId` | Not mandatory | **Mandatory** | Not mandatory |
| `customerId` (Quotes) | Optional (can be lead) | Optional | Optional |
| `customerId` (Orders/DN/Invoices) | Mandatory | Mandatory | Mandatory |

### Spain-Only Fields (IRPF & Equivalence Surcharge)

These fields appear on **all sales document types** (quotes, orders, delivery notes, invoices):

**Line-level fields:**
| Field | Type | Description |
|-------|------|-------------|
| `withholdingPercentage` | Decimal | IRPF withholding rate applied |
| `totalWithholding` | Decimal | IRPF withholding amount per line |
| `totalLiquidNoWithholding` | Decimal | Line total excluding IRPF |
| `equivalenceSurchargePercentage` | Decimal (read-only) | Surcharge % (if customer has `hasEquivalenceSurcharge=true`) |

**Document-level totals:**
| Field | Type | Description |
|-------|------|-------------|
| `totalWithholding` | Decimal | Total IRPF withholding at document level |
| `totalLiquidNoWithholding` | Decimal | Document total excluding IRPF |
| `totalFeeSurcharge` | Decimal | Total equivalence surcharge |
| `totalVatFee` | Decimal | VAT base for surcharge calculation |
| `hasEquivalenceSurcharge` | Boolean | Whether customer subject to regime |

> **FR & DE:** `totalFeeSurcharge`, `totalVatFee`, all withholding fields = unused (always 0).

### Equivalence Surcharge Rates (Spain)

| VAT Rate | Surcharge Rate |
|----------|---------------|
| 21% | 5.2% |
| 10% | 1.4% |
| 4% | 0.5% |

---

## 4. Products

| Field | FR | ES | DE |
|-------|----|----|-----|
| `useWithholding` | Unused | Boolean (IRPF applies) | Unused |
| `equivalenceSurchargePercentage` (ProductPriceService) | Always 0 | Calculated per customer regime | Always 0 |

---

## 5. Purchase Invoices

| Rule | FR | ES | DE |
|------|----|----|-----|
| `hasCashVat` | Optional (Boolean) | N/A | Optional (Boolean) |
| XML file upload (AP Automation) | Not supported | Not supported | **Supported** |
| File formats | PDF, images | PDF, images | PDF, images, **XML** |

---

## 6. Accounting Accounts

### Account Code Structure

| Legislation | Length | Format | Example |
|-------------|--------|--------|---------|
| **FR** | 5-13 digits | Numeric only | 572000000, 606000000 |
| **ES** | 4-13 chars | First 3 numeric, rest alphanumeric | 4110000, 411DUPONT |
| **DE** | Exactly 4 digits | Numeric only | 1530, 3010 |

### Account Level Hierarchy

**FR:**
- Only `Subaccount` level used for transactions (detail accounts, 5-13 digits)

**ES:**
- Only `Subaccount` level used for transactions (detail accounts)
- E.C.P.N class available (classes 8 and 9 for financial statements in net worth)

**DE:**
- `Transactional Account` (3-30 digits) = movement allowed
- `Aggregating Account` (3-29 digits) = no movement (grouping only)

### Account Type Classification

| accountType | FR | ES | DE |
|-------------|----|----|-----|
| Balance sheet | Classes 1-5 | Classes 1-5 | Classes 0, 1, 7, 3970, 3980 |
| Income statement | Classes 6-7 | Classes 6-7 | Classes 2, 3, 4, 5, 6, 8 (except 3970, 3980) |
| Special | Classes 8-9 | N/A | N/A |
| E.C.P.N | N/A | Classes 8-9 | N/A |
| Statistical | N/A | N/A | Class 9 |

### SubAccountType — Available Values

**FR only:**
- DEPRECIATION_PROVISION, STOCK, RESULT_BALANCE_SHEET, TITLE
- CUSTOMER_PREPAYMENT_TAX_INCLUDED, SUPPLIER_PREPAYMENT_TAX_INCLUDED, INPUT_TAX, OUTPUT_TAX

**ES only:**
- CUSTOMER_RETURNABLE_PACKAGING, SUPPLIER_RETURNABLE_PACKAGING, INPUT_TAX, OUTPUT_TAX

**DE only:**
- CURRENT_ASSETS, BORROWED_CAPITAL, COST, REDUCTION_IN_EXPENSES
- NEUTRAL_REVENUE, NEUTRAL_EXPENSES, SALES_DEDUCTION
- EXCHANGE_RATE_INCOME, EXCHANGE_RATE_EXPENSE, BALANCE_CARRIED_FORWARD
- AGGREGATE_CARRIED_FORWARD, OTHER_BALANCE_SHEET_ACCOUNT
- STATISTICAL_BALANCE, STATISTICAL_CONSUMPTION
- CURRENT_ASSETS_INPUT_TAX, BORROWED_CAPITAL_OUTPUT_TAX
- CUSTOMER_PREPAYMENT, SUPPLIER_PREPAYMENT

**Common (all legislations):**
- UNDEFINED, CUSTOMER, PROVIDER, BANK, CASH, CHARGE, REVENUE, CAPITAL, FIXED_ASSET, EMPLOYEE, MANAGEMENT_RESULT (FR+DE)

### German-Specific Account Fields

| Field | Type | Description |
|-------|------|-------------|
| `datevAutomaticAccount` | Boolean | Account auto-managed by DATEV |
| `datevAccountNumber` | String | DATEV account number |
| `includedEurForGermany` | Boolean (read-only) | Included in EÜR (Einnahmenüberschussrechnung) |
| `createdByUser` | Boolean (read-only) | Manually created (vs template) |

### French-Specific Account Fields

| Field | Type | Description |
|-------|------|-------------|
| `transitionalPrepaymentAccountId` | UUID | Prepayment account for advance payments with tax |

### Plan Master Templates

| Legislation | Available Templates |
|-------------|-------------------|
| FR | General chart of accounts 2024, General chart of accounts 2025, General chart of accounts for SMEs |
| ES | General chart of accounts |
| DE | DATEV SKR 03, DATEV SKR 04 |

---

## 7. Accounting Entries

### Mandatory Fields by Legislation

| Field | FR | ES | DE |
|-------|----|----|-----|
| `documentNumber` | Optional | Optional | **Mandatory** |
| `sessionId` | N/A | N/A | **Mandatory** (auto-assigned if not provided) |
| `tanNumber` | N/A | N/A | **Mandatory** (auto-increments) |
| `isCashVat` | Optional | N/A | Optional |

### Corrective Invoice Fields (ES only)

| Field | Type | Description |
|-------|------|-------------|
| `creditNoteType` | Enum | UNDEFINED, BY_DIFFERENCES, SUBSTITUTIVE |
| `creditNoteReason` | Enum | UNDEFINED, R1, R2, R3, R4, R5 (per Real Decreto 1619/2012) |
| `originalEntryInvoiceTaxesBaseSum` | Decimal | Base amount of original invoice |
| `originalAccountingEntryInvoiceTaxesAmount` | Decimal | Tax amount of original invoice |

### Equivalence Surcharge Fields in Entries (ES only)

| Field | Type | Description |
|-------|------|-------------|
| `hasEquivalenceSurcharge` | Boolean | Whether surcharge applies |
| `equivalenceSurchargePercentage` | Decimal | Surcharge percentage |
| `equivalenceSurchargeQuota` | Decimal | Optional fixed amount |

### German-Specific Entry Fields

| Field | Type | Description |
|-------|------|-------------|
| `contraAccount` | Account ref | General offset account per line |

---

## 8. Accounting Reports

| Field/Parameter | FR | ES | DE |
|-----------------|----|----|-----|
| `showClosingEntries` | Not used | Available (include closing entries) | Not used |
| KPI formulas | Adapted per FR standards | Adapted per ES standards | Adapted per DE standards |

---

## 9. Organization & Reference Data

### German-Specific Organization Fields

| Field | Type | Description |
|-------|------|-------------|
| `datevConsultantNumber` | String | DATEV consultant ID |
| `datevClientNumber` | String | DATEV client ID |
| `taxAuditExportNotes` | String | Notes for tax audit export (GoBD) |

### Cash VAT Configuration

| Legislation | Available | Notes |
|-------------|-----------|-------|
| FR | Yes | Configure per organization |
| ES | No | N/A |
| DE | Yes | Configure per organization |

### Taxes & Tax Groups

- Taxes are **standardized per legislation** and cannot be modified.
- Defined by name, percentages, and associated group reflecting legal requirements.

### Country Zip Code Availability

Only available for countries corresponding to supported legislations: FR, ES, DE, PT.

---

## 10. Summary: Key Differences at a Glance

| Concept | FR | ES | DE |
|---------|----|----|-----|
| **Identification** | SIRET/SIREN | NIF/DNI/NIE | Steuer-IdNr |
| **VIES document type** | TVA Intracommunautaire | NIF/IVA | USt-IdNr |
| **Withholding tax (IRPF)** | No | Yes | No |
| **Equivalence surcharge** | No | Yes (retail) | No |
| **Cash VAT** | Yes | No | Yes |
| **Auto-numbering (codes)** | Optional | Optional | Mandatory |
| **Collective accounts** | Optional | Optional | Mandatory |
| **DATEV integration** | No | No | Yes |
| **Account code format** | 5-13 digits | 4-13 alphanumeric | 4 digits |
| **Employees via API** | Yes | Yes | No |
| **Corrective invoice types** | No | Yes (R1-R5) | No |
| **XML file import** | No | No | Yes |
| **Accounting sessions** | No | No | Mandatory |
| **contraAccount (entries)** | No | No | Yes |
| **showClosingEntries (reports)** | No | Yes | No |
| **Sales doc: socialName** | Mandatory | Not mandatory | Not mandatory |
| **Sales doc: documentId** | Not mandatory | Mandatory | Not mandatory |
| **Sales doc: vatNumber (VIES)** | Mandatory | Not mandatory | Not mandatory |

---

## 11. Important Notes

### TenantId (DEPRECATED)
- `X-TenantId` header is no longer required and is completely ignored.
- Only `X-OrganizationId` is now needed (mandatory for most operations).
- Resources `countries`, `zipCodes`, `currencies`, `documentTypes` now require `X-OrganizationId` to resolve legislation.

### documentTypeId Auto-Assignment
- `documentTypeId` is automatically set based on legislation + country combination.
- Rarely needs manual assignment during creation.
- Ensures compliance with local identification requirements.

### Code Ranges (DE Legislation)
| Entity | Numeric Range |
|--------|---------------|
| Customers | 10000 - 69999 |
| Suppliers | 70000 - 99999 |
| Employees | N/A (not available in DE) |
