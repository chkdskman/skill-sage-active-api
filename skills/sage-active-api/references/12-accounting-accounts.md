# Accounting Accounts & Plan Master — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/accountingaccounts
> - https://developer.sage.com/sageactive/resources/accountingplanmaster
> - https://developer.sage.com/sageactive/resources/accounting

## Overview

This section is dedicated to the accounting resources managed within the context of an organization. Accounting is the backbone of financial management and compliance in an organization. The resources described under this chapter are critical for various accounting operations such as general ledger management, tax calculation, and accounting entries.

### Resources

- **Accounting Accounts** — Chart of accounts, known as `AccountingAccount` in the API context and Ledger account in the product interface. This resource serves as the framework for financial accounts within accounting operations. It defines both standard and custom accounts, crucial for categorizing transactions. Unlike the `AccountingEntry`, which records transactions, this resource focuses on defining various account types.
- **Accounting Plan Master** — The `accountingPlanMaster` resource represents the standard templates of accounting plans delivered within the system. These templates are specific to the legislations of FR (France), ES (Spain), or DE (Germany), reflecting the reference accounting plan models for each jurisdiction. These models are distinct from the `accountingaccount` resource, and if a model is not imported, its associated accounts will not be available within the organization. The primary purpose of this resource is to retrieve the template of an accounting plan to identify the standard accounting plans used in Sage Active and also to allow the importation of a template into an empty accounting plan. It facilitates aligning the accounting practices with the legal standards of the respective country, ensuring accuracy and compliance but does not have any other utility within the system.
- **Accounting Exercises** — Corresponds to the fiscal years within the field of accounting, known as `AccountingExercise` in the API context and Fiscal years in the product interface. A fiscal year, or accounting period, is a specific timeframe used by governments and businesses for accounting purposes to compare financial results and statements. An exercise can be in one of two states:
  - **Open**: During an open exercise, entries can be made or modified, reflecting the ongoing transactions and adjustments within the given fiscal year.
  - **Closed**: Once an exercise is closed, the entries are finalized, and no further modifications to the accounting records for that fiscal year are permitted.
- **Journal Types** — Define the various journals used during the entry of accounting documents, known as `JournalType` in the API context and Journal in the product interface. Each journal is associated with a specific type:
  - **sales**: Used for entering customer sales accounting documents, providing access to deadlines and taxes.
  - **purchases**: Used for entering supplier purchase accounting documents, providing access to deadlines and taxes.
  - **financial**: Used for entering customer/supplier payments and other banking operations.
  - **general**: Used for entering miscellaneous accounting documents.
  - **carry forward**: Used at the close of the fiscal year to generate carry forward entries.
- **Taxes** — Represents the legal taxes in effect within specific legislations (such as FR, ES, or DE). These taxes are standard for each legislation and, therefore, cannot be modified. Each tax is defined by its name, percentages, and an associated group, reflecting the legal requirements and tax structures within the given legislation. The management of these standardized taxes is essential for ensuring compliance with legal tax obligations and accurately reflecting the current tax laws within the accounting and sales domain.
- **Taxes Treatments** — Used to define tax codes that associate the type of operation (such as purchasing goods, purchasing services, selling goods, etc.) with general tax accounts. This association simplifies the entry of sales or purchase records within the accounting system. For example, when entering a sales invoice, if the net sales account is linked to a tax code, the associated tax account will be automatically proposed. The calculation of the net and VAT will be automatic according to the percentage of the tax.
- **Accounting Entries** — Accounting entries, known as `AccountingEntry` in the API context and Journal entries in the product interface, refer to the detailed transactions that accurately record the movement of funds, assets, liabilities, and equity within an organization.
  - These entries serve as crucial components of a company's financial records, capturing various financial activities such as revenue generation, expense allocation, and asset acquisitions.
  - Accounting entries are categorized and organized into journals based on their specific characteristics, and these characteristics are determined by the associated journal.
  - An accounting entry must be balanced, ensuring that debits equal credits.
  - This means that accounting entries are complete accounting documents rather than individual lines of entries.
- **Balances** — Includes `AccountingTrialBalance`, `AccountingThirdPartyBalance`, `AccountingBalanceSheet`, and `AccountingProfitAndLoss` services:
  - The `AccountingTrialBalance` service provides a trial balance report, generating a summary of all debits and credits for a clear view of the financial balance over a specified period.
  - The `AccountingThirdPartyBalance` service provides a balance overview for third-party accounts, compiling detailed transaction records across all accounts associated with third parties.
  - The `AccountingBalanceSheet` service provides a balance sheet report, generating a structured view of assets and liabilities for a clear snapshot of the financial position over a specified period.
  - The `AccountingProfitAndLoss` service provides a profit and loss report, generating a structured view of revenues and expenses for analysis of financial performance over a specified period.
- **Analytic Dimensions** — An analytic dimension represents a customizable axis used to categorize accounting entry lines for analytical purposes.
  - Typical examples include dimensions such as Warehouse, Department, or Project.
  - Each dimension is configured with a list of tags, which define the allowed values for that dimension (e.g., the Warehouse dimension may include tags such as Lyon, Toulouse, and Rouen).
  - When creating accounting entries, you can allocate each line across one or more analytic dimensions by assigning a specific tag from the list defined for each dimension. See `AccountingEntryDimensionTag`.
  - You can also configure accounts in the chart of accounts to assign default dimension tags, which will be automatically applied to entry lines using those accounts. See `AccountingAccountDimensionTag`.

---

## Accounting Accounts

### HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | `createAccountingAccount` | `AccountingAccountCreateGLDtoInput` |
| POST | Update | Mutation | `updateAccountingAccount` | `AccountingAccountUpdateGLDtoInput` |
| POST | Delete | Mutation | `deleteAccountingAccount` | `AccountingAccountDeleteGLDtoInput` |
| POST | Read | Query | `accountingAccounts` filtered by id | — |
| POST | List | Query | `accountingAccounts` | — |

### Description

Chart of accounts, known as `AccountingAccount` in the API context and Ledger account in the product interface. This resource serves as the framework for financial accounts within accounting operations. It defines both standard and custom accounts, crucial for categorizing transactions. Unlike the `AccountingEntry`, which records transactions, this resource focuses on defining various account types.

Please note that the chart of accounts may vary based on specific legal requirements, such as those in France, Spain, or Germany.

It is possible to create an account easily by simply providing its code and name. You can even create an account using only its code. In this case, a generic localized label will be generated based on the legislation, following the format: `Automatically created account for code: nnnnnn`.

Finally, you can also create an account by specifying additional values such as its Level or Type.

#### Create Account Example

GraphQL Mutation:

```graphql
mutation ($values: AccountingAccountCreateGLDtoInput!) {
  createAccountingAccount(input: $values) {
    id
  }
}
```

GraphQL Variables:

```json
{
  "values": {
    "code": "626200",
    "name": "Phone"
  }
}
```

Example Response:

```json
{
  "data": {
    "createAccountingAccount": {
      "id": "e451aff7-074f-4e72-a6fb-4ea25e9d9c64"
    }
  }
}
```

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer `{access_token}` |
| X-TenantId | *(Deprecated)* Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### Fields: `accountingAccounts`

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id. System field that cannot be assigned. |
| creationDate | DateTime | System | Creation Date. System field that cannot be assigned. |
| modificationDate | DateTime | System | Modification Date. System field that cannot be assigned. |
| code | String | Required, Non-updatable | Code. Variable length, see Info section for rules per legislation. |
| name | String (100) | Required | Name. |
| description | String | Computed | Computed field (Code + Name). Cannot be used in filtering or ordering. |
| deactivated | Boolean | No | Deactivated. See Info section. |
| accountLevel | Enum | Non-updatable | Level. Values: `UNDEFINED`, `ACCOUNT`, `BREAKDOWN`, `GROUP`, `SUB_ACCOUNT`, `SUB_GROUP`, `TOTAL`. See Info section. |
| subAccountType | Enum | No | Nature of account. See Info section for allowed values per legislation. |
| accountType | Enum | No | Category. Values: `UNDEFINED`, `BALANCE_SHEET`, `ECPN`, `INCOME_STATEMENT`, `SPECIAL`, `STATISTICAL`. See Info section. |
| datevAutomaticAccount | Boolean | No | Managed automatically by DATEV. See Info section. |
| datevAccountNumber | String | No | DATEV account number used for German bookkeeping. See Info section. |
| applyVat | Boolean | No | Indicates whether VAT is applied based on the account's nature. See Info section. |
| includedEurForGermany | Boolean | Read-only | Indicates whether the account is considered in the EÜR result (German legislation only). See Info section. |
| createdByUser | Boolean | Read-only | Indicates when the account is created manually (German legislation only). See Info section. |
| transitionalPrepaymentAccount | AccountingAccount | Dataloader | Fields of AccountingAccount. |
| transitionalPrepaymentAccountId | UUID | No | Prepayment account for advance payments with tax. See Info section. |
| yearEndClosingAccount | AccountingAccount | Dataloader | Fields of AccountingAccount. |
| yearEndClosingAccountId | UUID | No | yearEndClosingAccount. See Info section. |
| taxTreatment | TaxTreatment | Dataloader | Fields of TaxTreatment (related to tax Code). |
| taxTreatmentId | UUID | No | Tax Treatment id. See Info section. |
| accountingAccountDimensionTags[] | Array | No | Default Analytic dimension and selected tag. |

### Info: `accountingAccounts`

#### code

This explanation pertains to accounts where the `accountLevel` is Subaccount. Refer to `accountLevel` for the structure of other account types.

- **FR**: Length: Variable, between 4 and 13 alphanumeric characters. First 3 must be numeric. Examples: `4110000`, `411DUPONT`, `445662000`, `445663`, `6010`.
- **ES**: Length: Composed of a fixed length between 5 and 13 digits. Example: `572000000`, `606000000`.
- **DE**: Length: Exactly 4 digits. Example: `1530`, `3010`.

#### deactivated

Deactivating an accounting account means that the account is not available for generating new accounting entries. But it will still be displayed in all the information that has been generated using that account.

#### accountLevel

**FR (France):**

| Value | Description |
|-------|-------------|
| Group | Accounting account of Total. Only 1 digit is allowed. Represents an element of the organization's financial statement. |
| Subgroup | Accounting account of Total. Composed of 2 digits. It's a division of the financial statement element. |
| Account | Accounting account of Total. Composed of 3 digits. These are the accounts defined according to each country's chart of accounts. |
| Breakdown | Accounting account of Total. Composed of 4 digits. It's a division of the Account. |
| Subaccount | Accounting account of Detail. Composed of a fixed length between 5 and 13 digits. Only Subaccount accounts can be used in accounting entries. |
| Total | Used for the balance guides. From 1 to 3 digits. |

**ES (Spain):**

| Value | Description |
|-------|-------------|
| Group | Unused (For Spain only). |
| Subgroup | Unused (For Spain only). |
| Account | Unused (For Spain only). |
| Breakdown | Unused (For Spain only). |
| Subaccount | Accounting account of Detail. Only Subaccount accounts can be used in accounting entries. See `code` for account number structure rules. |
| Total | Unused (For France only). |

**DE (Germany):**

| Value | Description |
|-------|-------------|
| Group | Unused (For Spain only). |
| Subgroup | Unused (For Spain only). |
| Account | Unused (For Spain only). |
| Breakdown | Unused (For Spain only). |
| Subaccount | Accounting account. Composed of 4 digits. Only Subaccount accounts can be used in accounting entries. |
| Total | Unused (For France only). |

**PT (Portugal) — Example, must be qualified:**

| Value | Description |
|-------|-------------|
| Class | High-level classification. Only 1 digit is allowed. |
| General Ledger Account | Composed of 2 digits. Represents a subdivision of the high-level classification. |
| Aggregating Account | No movement allowed. Length between 3 and 29 digits. Used to group transactional accounts. |
| Transactional Account | With movement. Length between 3 and 30 digits. Only Transactional Account accounts can be used in accounting entries. |
| Total | Unused (For France only). |

#### subAccountType

Only used if `accountLevel` is set to `SUB_ACCOUNT`.

**FR (France) allowed values:**

`UNDEFINED`, `CUSTOMER`, `PROVIDER`, `BANK`, `CASH`, `CHARGE`, `REVENUE`, `CAPITAL`, `FIXED_ASSET`, `MANAGEMENT_RESULT`, `DEPRECIATION_PROVISION`, `STOCK`, `RESULT_BALANCE_SHEET`, `EMPLOYEE`, `TITLE`, `CUSTOMER_PREPAYMENT_TAX_INCLUDED`, `SUPPLIER_PREPAYMENT_TAX_INCLUDED`, `INPUT_TAX`, `OUTPUT_TAX`

**ES (Spain) allowed values:**

`UNDEFINED`, `CUSTOMER`, `PROVIDER`, `BANK`, `CASH`, `CHARGE`, `REVENUE`, `CAPITAL`, `FIXED_ASSET`, `EMPLOYEE`, `CUSTOMER_RETURNABLE_PACKAGING`, `SUPPLIER_RETURNABLE_PACKAGING`, `INPUT_TAX`, `OUTPUT_TAX`

**DE (Germany) allowed values:**

`UNDEFINED`, `CUSTOMER`, `PROVIDER`, `BANK`, `CASH`, `CHARGE`, `REVENUE`, `CAPITAL`, `FIXED_ASSET`, `MANAGEMENT_RESULT`, `EMPLOYEE`, `CURRENT_ASSETS`, `BORROWED_CAPITAL`, `COST`, `REDUCTION_IN_EXPENSES`, `NEUTRAL_REVENUE`, `NEUTRAL_EXPENSES`, `SALES_DEDUCTION`, `EXCHANGE_RATE_INCOME`, `EXCHANGE_RATE_EXPENSE`, `BALANCE_CARRIED_FORWARD`, `AGGREGATE_CARRIED_FORWARD`, `OTHER_BALANCE_SHEET_ACCOUNT`, `STATISTICAL_BALANCE`, `STATISTICAL_CONSUMPTION`, `CURRENT_ASSETS_INPUT_TAX`, `BORROWED_CAPITAL_OUTPUT_TAX`, `CUSTOMER_PREPAYMENT`, `SUPPLIER_PREPAYMENT`

#### accountType

**FR (France):**
- Balance sheet: account classes 1 to 5
- Income statement: account classes 6 and 7
- Special: account classes 8 and 9
- E.C.P.N: Unused (For Spain only)
- Statistical: Unused (For Germany only)

**ES (Spain):**
- Balance sheet: account classes 1 to 5
- Income statement: account classes 6 and 7
- Special: Unused (For France only)
- E.C.P.N: Financial statements in net worth classes 8 and 9
- Statistical: Unused (For Germany only)

**DE (Germany):**
- Balance sheet: account classes 0, 1, 7, 3970, 3980
- Income statement: account classes 2, 3, 4, 5, 6, 8 (except 3970 & 3980)
- Special: Unused (For France only)
- E.C.P.N: Unused (For Spain only)
- Statistical: account class 9

#### yearEndClosingAccountId

- **FR**: Use only when `accountType` is E.C.P.N.
- **ES**: Unused (For Spain only).
- **DE**: Unused (For Spain only).

#### applyVat

Indicates whether VAT should be applied to transactions associated with this account. The value is automatically determined based on the nature of the account and applies to all legislations if it is not defined. If the field is explicitly set to true or false, the provided value will override the automatic behavior.

#### datevAutomaticAccount / datevAccountNumber

- **FR**: Unused (For Germany only).
- **ES**: Unused (For Germany only).
- **DE**:
  - `datevAutomaticAccount`: Indicates whether the account is automatically managed by DATEV. If true, the account is controlled by DATEV and should not be manually modified. If false, the account can be manually managed.
  - `datevAccountNumber`: DATEV account number used to integrate with the German accounting system DATEV. This field is populated based on the nature of the account and ensures compliance with DATEV standards.

#### transitionalPrepaymentAccountId

Refers to an account used for advance payments including tax. Required for accounts with the nature "Customer advance payment (tax included)" or "Supplier advance payment (tax included)".

- **FR**: Unused (For France only).
- **DE**: Unused (For France only).

#### includedEurForGermany

- **FR**: Unused (For Germany only).
- **ES**: Unused (For Germany only).
- **DE**: Indicates whether the account is included in the EÜR (Einnahmenüberschussrechnung - simplified income statement). `true` means the account is included in the EÜR calculation/export; `false` means it is excluded. Typical usage: include sales, bank, and expense accounts; exclude fixed assets and equity accounts.

#### createdByUser

- **FR**: Unused (For Germany only).
- **ES**: Unused (For Germany only).
- **DE**: This field is required to prove the origin of every account in the chart of accounts under German accounting law. It clearly distinguishes standard system accounts from user-created ones, allowing auditors to verify that the official chart (SKR03 or SKR04) remains intact and GoBD compliant.

#### taxTreatmentId

`taxTreatment` is named "tax Code" in the Chart of Accounts Interface.

### Fields: `accountingAccountDimensionTags`

This section describes the default analytic allocation configured for an accounting account. It allows you to define, per account, a default dimension and selected tag for each analytic axis (e.g. Warehouse -> Toulouse).

**Example:** Suppose you want to ensure that whenever this account is used in an entry line, the following analytic context is preselected:
- Warehouse -> Lyon
- Department -> Sales

These values will be automatically suggested or pre-filled during entry creation, though they can be overridden manually or by automated rules.

See also: `AccountingEntryDimensionTag`, `Analytic Dimensions`.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id. System field that cannot be assigned. |
| creationDate | DateTime | System | Creation Date. System field that cannot be assigned. |
| modificationDate | DateTime | System | Modification Date. System field that cannot be assigned. |
| dimensionId | UUID | No | Id of the analytic dimension (e.g. Warehouse). Refers to the analytic category to which the tag belongs. |
| dimensionTagId | UUID | No | Id of the selected default tag. Represents the default tag selected for this dimension. |

> **Warning:** Analytic dimension and tag information is currently read-only. You cannot assign or modify these values through the API at this stage. Dataloaders are not currently available for analytic dimensions and dimensionTags. This means that if you need to retrieve additional details (e.g., dimension name for a given dimensionId), you must manually correlate the values using the identifiers (id fields) returned by the API.

---

## Accounting Plan Master

### HTTP Operations

| HTTP | Operation | Type | Object |
|------|-----------|------|--------|
| POST | Read | Action (Query) | `accountingPlansMasterById` |
| POST | Create | Action (Mutation) | `ImportAccountingPlanMaster` |

> **Note on Action type:** The action resource represents a type of object that differs from standard queries or mutations. For a query, it returns a result already prepared by Sage Active, with no possibility to add filters, sorts, or explore the data further, or it executes an action (e.g., sending an email). For a mutation, it executes a specific business action, also carried out by Sage Active, which will impact the data (e.g., generating a credit note from an invoice).

### Description

The `accountingPlanMaster` resource represents the standard templates of accounting plans delivered within the system. These templates are specific to the legislations of FR (France), ES (Spain), or DE (Germany), reflecting the reference accounting plan models for each jurisdiction.

These models are distinct from the `accountingaccount` resource, and if a model is not imported, its associated accounts will not be available within the organization.

The primary purpose of this resource is to retrieve the template of an accounting plan to identify the standard accounting plans used in Sage Active and also to allow the importation of a template into an empty accounting plan.

It facilitates aligning the accounting practices with the legal standards of the respective country, ensuring accuracy and compliance but does not have any other utility within the system.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer `{access_token}` |
| X-TenantId | *(Deprecated)* Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

> **Tip:** Pagination is typically required for queries that return multiple records. However, for the `accountingPlansMasterById` query, pagination is not necessary as this query returns all accounts from the template without the need to manage pagination.

### Fields: `accountingPlansMasterById`

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System, Read-only | Predefined template ID. See Info section. |
| creationDate | DateTime | System | Creation Date. |
| modificationDate | DateTime | System | Modification Date. |
| description | String | Read-only | Template Name. |
| accountingPlanMasterLines[] | Array | Read-only | List of accounting accounts from the template. |

#### Info: Template IDs

- Id General chart of accounts 2024 (FR)
- Id General chart of accounts 2025 (FR)
- Id General chart of accounts DATEV SKR 03 (DE)
- Id General chart of accounts DATEV SKR 04 (DE)
- Id General chart of accounts (ES)
- Id General chart of accounts for SMEs (ES)

### Fields: `accountingPlanMasterLines`

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System, Read-only | Id. |
| creationDate | DateTime | System | Creation Date. |
| modificationDate | DateTime | System | Modification Date. |
| code | String | Read-only | Code. |
| name | String | Read-only | Name. |
| accountLevel | Enum | Read-only | Level. Values: `UNDEFINED`, `ACCOUNT`, `BREAKDOWN`, `GROUP`, `SUB_ACCOUNT`, `SUB_GROUP`, `TOTAL`. See Info section. |
| subAccountType | Enum | Read-only | Nature of account. See Info section for allowed values per legislation. |
| accountType | Enum | Read-only | Category. Values: `UNDEFINED`, `BALANCE_SHEET`, `ECPN`, `INCOME_STATEMENT`, `SPECIAL`, `STATISTICAL`. See Info section. |
| applyVat | Boolean | No | Indicates whether VAT is applied based on the account's nature. |

### Info: `accountingPlanMasterLines`

#### accountLevel

**FR (France):**

| Value | Description |
|-------|-------------|
| Group | Unused (For Spain only). |
| Subgroup | Unused (For Spain only). |
| Account | Unused (For Spain only). |
| Breakdown | Unused (For Spain only). |
| Subaccount | Accounting account of Detail. Composed of minimum 6 or more digits. Subaccount can be used only to generate accounting entries. |
| Total | Used for the balance guides. |

**ES (Spain):**

| Value | Description |
|-------|-------------|
| Group | Unused (For Spain only). |
| Subgroup | Unused (For Spain only). |
| Account | Unused (For Spain only). |
| Breakdown | Unused (For Spain only). |
| Subaccount | Accounting account of Detail. Composed of minimum 6 or more digits. Subaccount can be used only to generate accounting entries. |
| Total | Unused (For France only). |

**DE (Germany):**

| Value | Description |
|-------|-------------|
| Group | Accounting account of Total. Only 1 digit is allowed. Represents an element of the organization's financial statement. |
| Subgroup | Accounting account of Total. Composed of 2 digits. It's a division of the financial statement element. |
| Account | Accounting account of Total. Composed of 3 digits. These are the accounts defined according to each country's chart of accounts. |
| Breakdown | Accounting account of Total. Composed of 4 digits. It's a division of the Account. |
| Subaccount | Accounting account of Detail. Composed of minimum 5 or more digits. Subaccount can be used only to generate accounting entries. |
| Total | Unused (For France only). |

#### subAccountType

Only used if `accountLevel` is set to `SUB_ACCOUNT`.

**FR (France) allowed values:**

`UNDEFINED`, `CUSTOMER`, `PROVIDER`, `BANK`, `CASH`, `CHARGE`, `REVENUE`, `CAPITAL`, `FIXED_ASSET`, `MANAGEMENT_RESULT`, `DEPRECIATION_PROVISION`, `STOCK`, `RESULT_BALANCE_SHEET`, `EMPLOYEE`, `TITLE`, `CUSTOMER_PREPAYMENT_TAX_INCLUDED`, `SUPPLIER_PREPAYMENT_TAX_INCLUDED`, `INPUT_TAX`, `OUTPUT_TAX`

**ES (Spain) allowed values:**

`UNDEFINED`, `CUSTOMER`, `PROVIDER`, `BANK`, `CASH`, `CHARGE`, `REVENUE`, `CAPITAL`, `FIXED_ASSET`, `EMPLOYEE`, `CUSTOMER_RETURNABLE_PACKAGING`, `SUPPLIER_RETURNABLE_PACKAGING`, `INPUT_TAX`, `OUTPUT_TAX`

**DE (Germany) allowed values:**

`UNDEFINED`, `CUSTOMER`, `PROVIDER`, `BANK`, `CASH`, `CHARGE`, `REVENUE`, `CAPITAL`, `FIXED_ASSET`, `MANAGEMENT_RESULT`, `EMPLOYEE`, `CURRENT_ASSETS`, `BORROWED_CAPITAL`, `COST`, `REDUCTION_IN_EXPENSES`, `NEUTRAL_REVENUE`, `NEUTRAL_EXPENSES`, `SALES_DEDUCTION`, `EXCHANGE_RATE_INCOME`, `EXCHANGE_RATE_EXPENSE`, `BALANCE_CARRIED_FORWARD`, `AGGREGATE_CARRIED_FORWARD`, `OTHER_BALANCE_SHEET_ACCOUNT`, `STATISTICAL_BALANCE`, `STATISTICAL_CONSUMPTION`, `CURRENT_ASSETS_INPUT_TAX`, `BORROWED_CAPITAL_OUTPUT_TAX`, `CUSTOMER_PREPAYMENT`, `SUPPLIER_PREPAYMENT`

#### accountType

**FR (France):**
- Balance sheet: account classes 1 to 5
- Income statement: account classes 6 and 7
- Special: account classes 8 and 9
- ECPN: Unused (For Spain only)

**ES (Spain):**
- Balance sheet: account classes 1 to 5
- Income statement: account classes 6 and 7
- Special: Unused (For France only)
- ECPN: Unused (For Spain only)

**DE (Germany):**
- Balance sheet: account classes 1 to 5
- Income statement: account classes 6 and 7
- Special: Unused (For France only)
- ECPN: Financial statements in net worth

#### applyVat

Indicates whether VAT should be applied to transactions associated with this account. The behavior depends on the nature of the account and applies to all legislations.

### ImportAccountingPlanMaster (Deprecated)

> **Deprecated:** Since a chart of accounts is now automatically imported through an organization's onboarding, even though this operation is still available, it is no longer relevant.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| accountingPlanMasterId | UUID | System | Id. |
| subAccountLength | Int | Required | Code. |
