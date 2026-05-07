# Accounting Reports — Trial Balance, Balance Sheet, P&L, KPIs

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/accountingtrialbalance
> - https://developer.sage.com/sageactive/resources/accountingthirdpartybalance
> - https://developer.sage.com/sageactive/resources/accountingbalancesheet
> - https://developer.sage.com/sageactive/resources/accountingprofitandloss
> - https://developer.sage.com/sageactive/resources/accountingreportpositions
> - https://developer.sage.com/sageactive/resources/accountingkpis

---

## accountingTrialBalance

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Read | Action (Query) | accountingTrialBalance | AccountingTrialBalanceGLDtoInput |

### Description

The AccountingTrialBalance service provides a trial balance report for accounting. This service generates a summary of all debits and credits, providing a clear view of the financial balance over a specified period.

### Functionality

- **Balance Generation:** Returns a detailed list of accounts with their balances.
- **Customizable Date Range:** Allows selection of start and end dates for the report.
- **Account Filter:** Optionally filters the report by account codes.
- **Enhanced Options:** Offers options to exclude zero balances, compare trial balances, show temporary carryforwards, and closing entries.

### Input Parameters

- **Report Range:** Optionally, specify the range of account codes to include in the report between `StartAccountCode` and `EndAccountCode`.
- **Fiscal Exercise:** Mandatory, identify the fiscal exercise for the report using `ExerciseId` the unique identifier of the fiscal exercise.
- **Report Dates:** Mandatory, set the date range for the report using `StartDate` and `EndDate`.
- **Balance Filters:** Optionally (default value `false`), configure additional options for balance inclusion:
  - `IsWithoutZeroBalance`: Whether to exclude third parties with zero balance.
  - `IsBalanceComparative`: Whether to include comparative balance data.
  - `ShowTemporaryCarryForward`: Whether to include temporary carryforwards.
  - `ShowClosingEntries`: Whether to include closing entries (Spain legislation only).

### Response

The service returns an array of `AccountingTrialBalanceOutputGLDto` objects, each representing an account with the following fields:

- **Account Details:** `AccountDescription` and `AccountCode`
- **Accumulated Amounts:** Represent amounts for the entire fiscal exercise, available only if `IsBalanceComparative` is set to true. `AccumulatedCreditAmount` and `AccumulatedDebitAmount`
- **Period Amounts:** Represent amounts for the specified period. `CreditAmount` and `DebitAmount`
- **Balances:** Represent the debit/credit balances. `AccumulatedBalance` and `Balance`
- **Signed Values:** Correspond to the previous values but return signed values. `AccumulatedCreditBalance`, `AccumulatedDebitBalance`, `CreditBalance`, `DebitBalance`
  - When `CreditAmount = 1200` then `CreditBalance = -1200`
  - When `DebitAmount = 1500` then `DebitBalance = 1500`

> **Tips and Tricks:** Pagination is typically required for queries that return multiple records. However, for the `accountingTrialBalance` query, pagination is not necessary because this query returns an array that includes all accounts and their totals.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### GraphQL Query

```graphql
query($input: AccountingTrialBalanceGLDtoInput!) {
  accountingTrialBalance(input: $input) {
    accountCode
    accountDescription
    accountType
    accumulatedBalance
    accumulatedCreditAmount
    accumulatedCreditBalance
    accumulatedDebitAmount
    accumulatedDebitBalance
    balance
    creditAmount
    creditBalance
    debitAmount
    debitBalance
  }
}
```

### All Available GraphQL Variables

```json
{
  "input": {
    "startAccountCode": "1000000",
    "endAccountCode": "3999999",
    "exerciseId": "458e1d68-faff-4e02-8ae0-4843d05e00da",
    "startDate": "2022-12-01",
    "endDate": "2022-12-31",
    "isWithoutZeroBalance": false,
    "isTrialBalanceComparative": false,
    "showTemporaryCarryForward": false,
    "showClosingEntries": false
  }
}
```

### Minimum Required GraphQL Variables

```json
{
  "input": {
    "exerciseId": "74396520-b798-4a75-b098-a55427d62b5a",
    "startDate": "2024-05-01",
    "endDate": "2024-05-31"
  }
}
```

### Example Response

```json
{
  "data": {
    "accountingTrialBalance": [
      {
        "accountCode": "311000000",
        "accountDescription": "311000000 - Stock de matières 1ères",
        "accountType": "BALANCE_SHEET",
        "accumulatedBalance": 0,
        "accumulatedCreditAmount": 0,
        "accumulatedCreditBalance": 0,
        "accumulatedDebitAmount": 0,
        "accumulatedDebitBalance": 0,
        "balance": -1275852.54,
        "creditAmount": 1777965.18,
        "creditBalance": -1777965.18,
        "debitAmount": 502112.64,
        "debitBalance": 502112.64
      },
      {
        "accountCode": "355000000",
        "accountDescription": "355000000 - Stock de produits finis",
        "accountType": "BALANCE_SHEET",
        "accumulatedBalance": 0,
        "accumulatedCreditAmount": 0,
        "accumulatedCreditBalance": 0,
        "accumulatedDebitAmount": 0,
        "accumulatedDebitBalance": 0,
        "balance": -59480.00,
        "creditAmount": 255080.00,
        "creditBalance": -255080.00,
        "debitAmount": 195600.00,
        "debitBalance": 195600.00
      }
    ]
  }
}
```

### accountingTrialBalance Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| startAccountCode | String | No | Beginning account code (max 13) |
| endAccountCode | String | No | Ending account code (max 13) |
| exerciseId | UUID | Yes | Exercise identifier |
| startDate | DateTime | Yes | Start date of the report (date only, time ignored) |
| endDate | DateTime | Yes | End date of the report (date only, time ignored) |
| isWithoutZeroBalance | Boolean | No | Exclude accounts with zero balance |
| isTrialBalanceComparative | Boolean | No | Compare with cumulative values |
| showTemporaryCarryForward | Boolean | No | Include temporary carryforwards |
| showClosingEntries | Boolean | No | Include closing entries (not used for France or Germany; Spain: true = include closing entry lines) |

### accountingTrialBalance Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| accountCode | String | — | Code of the account (max 13) |
| accountDescription | String | — | Description of the account (max 100) |
| accountType | Enum | — | Category of the account: `UNDEFINED`, `BALANCE_SHEET`, `ECPN`, `INCOME_STATEMENT`, `SPECIAL`, `STATISTICAL` |
| accumulatedBalance | Decimal | — | Accumulated balance of the account (only if `isTrialBalanceComparative` is true) |
| accumulatedCreditAmount | Decimal | — | Credit amount for the entire fiscal exercise (only if `isTrialBalanceComparative` is true) |
| accumulatedCreditBalance | Decimal | — | Signed credit amount for the entire fiscal exercise (only if `isTrialBalanceComparative` is true) |
| accumulatedDebitAmount | Decimal | — | Accumulated debit amount for the entire fiscal exercise (only if `isTrialBalanceComparative` is true) |
| accumulatedDebitBalance | Decimal | — | Signed accumulated debit balance for the entire fiscal exercise (only if `isTrialBalanceComparative` is true) |
| balance | Decimal | — | Net balance of the account |
| creditAmount | Decimal | — | Total credit amount within the specified period |
| creditBalance | Decimal | — | Signed credit balance within the specified period |
| debitAmount | Decimal | — | Total debit amount within the specified period |
| debitBalance | Decimal | — | Signed debit balance within the specified period |

> **accountType by legislation:**
> - **France:** Balance sheet = classes 1-5, Income statement = classes 6-7, Special = classes 8-9
> - **Spain:** Balance sheet = classes 1-5, Income statement = classes 6-7, ECPN = classes 8-9
> - **Germany:** Balance sheet = classes 0, 1, 7, 3970, 3980, Income statement = classes 2-6, 8 (except 3970/3980), Statistical = class 9

---

## accountingThirdPartyBalance

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Read | Action (Query) | accountingThirdPartyBalance | AccountingThirdPartyBalanceGLDtoInput |

### Description

The AccountingThirdPartyBalance service provides a balance overview for third-party accounts. This service compiles detailed transaction records across all accounts associated with third parties, allowing for comprehensive financial tracking over specified periods.

### Functionality

- **Balance Generation:** Returns a detailed list of third parties with their balances.
- **Customizable Date Range:** Allows selection of start and end dates for the report.
- **Third Party Filter:** Optionally filters the report by third party codes.
- **Enhanced Options:** Offers options to exclude zero balances and compare balances.

### Input Parameters

- **Report Range:** Optionally, specify the range of third party codes to include in the report between `ThirdPartyCodeFrom` and `ThirdPartyCodeTo`.
- **Third Party Type:** Mandatory, define the type of third party for which the report is generated using `ThirdPartyType` (customer, supplier, employee).
- **Fiscal Exercise:** Mandatory, identify the fiscal exercise for the report using `ExerciseId` the unique identifier of the fiscal exercise.
- **Report Dates:** Mandatory, set the date range for the report using `StartDate` and `EndDate`.
- **Balance Filters:** Optionally (default value `false`), configure additional options for balance inclusion:
  - `IsWithoutZeroBalance`: Whether to exclude third parties with zero balance.
  - `IsBalanceComparative`: Whether to include comparative balance data.
  - `ShowTemporaryCarryForward`: Whether to include temporary carryforwards.

### Response

The service returns an array of `AccountingThirdPartyBalanceOutputGLDto` objects, each representing a third party account with the following fields:

- **Third Party Details:** `ThirdPartyDescription` and `ThirdPartyId`
- **Accumulated Amounts:** Represent amounts for the entire fiscal exercise, available only if `IsBalanceComparative` is set to true. `AccumulatedCreditAmount` and `AccumulatedDebitAmount`
- **Period Amounts:** Represent amounts for the specified period. `CreditAmount` and `DebitAmount`
- **Balances:** Represent the debit/credit balances. `AccumulatedBalance` and `Balance`
- **Signed Values:** Correspond to the previous values but return signed values. `AccumulatedCreditBalance`, `AccumulatedDebitBalance`, `CreditBalance`, `DebitBalance`
  - When `CreditAmount = 1200` then `CreditBalance = -1200`
  - When `DebitAmount = 1500` then `DebitBalance = 1500`

> **Tips and Tricks:** Pagination is typically required for queries that return multiple records. However, for the `accountingThirdPartyBalance` query, pagination is not necessary because this query returns an array that includes all accounts and their totals.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### GraphQL Query

```graphql
query($input: AccountingThirdPartyBalanceGLDtoInput!) {
  accountingThirdPartyBalance(input: $input) {
    thirdPartyDescription
    thirdPartyId
    accumulatedBalance
    accumulatedCreditAmount
    accumulatedCreditBalance
    accumulatedDebitAmount
    accumulatedDebitBalance
    balance
    creditAmount
    creditBalance
    debitAmount
    debitBalance
  }
}
```

### All Available GraphQL Variables

```json
{
  "input": {
    "thirdPartyCodeFrom": "CARAT",
    "thirdPartyCodeTo": "OPALE",
    "thirdPartyType": "CUSTOMER",
    "exerciseId": "65f3cd39-9fb0-424d-bcaf-3fe2e88c9579",
    "startDate": "2024-05-01",
    "endDate": "2024-05-31",
    "isWithoutZeroBalance": false,
    "isBalanceComparative": false,
    "showTemporaryCarryForward": false
  }
}
```

### Minimum Required GraphQL Variables

```json
{
  "input": {
    "thirdPartyType": "CUSTOMER",
    "exerciseId": "65f3cd39-9fb0-424d-bcaf-3fe2e88c9579",
    "startDate": "2024-05-01",
    "endDate": "2024-05-31"
  }
}
```

### Example Response

```json
{
  "data": {
    "accountingThirdPartyBalance": [
      {
        "thirdPartyDescription": "CARAT - Carat S.a.r.l",
        "thirdPartyId": "65f3cd39-a1f0-424d-bcaf-3fe2e88c9579",
        "accumulatedBalance": 0,
        "accumulatedCreditAmount": 0,
        "accumulatedCreditBalance": 0,
        "accumulatedDebitAmount": 0,
        "accumulatedDebitBalance": 0,
        "balance": 51080.39,
        "creditAmount": 19000,
        "creditBalance": -19000,
        "debitAmount": 70080.39,
        "debitBalance": 70080.39
      },
      {
        "thirdPartyDescription": "COMPTOIR - Client divers",
        "thirdPartyId": "65f3cd39-a1a5-424d-bcaf-3fe2e88c9579",
        "accumulatedBalance": 0,
        "accumulatedCreditAmount": 0,
        "accumulatedCreditBalance": 0,
        "accumulatedDebitAmount": 0,
        "accumulatedDebitBalance": 0,
        "balance": -7023.43,
        "creditAmount": 115157.3,
        "creditBalance": -115157.3,
        "debitAmount": 108133.87,
        "debitBalance": 108133.87
      }
    ]
  }
}
```

### accountingThirdPartyBalance Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| thirdPartyCodeFrom | String | No | Starting third party code in the report range (max 20) |
| thirdPartyCodeTo | String | No | Ending third party code in the report range (max 20) |
| thirdPartyType | Enum | Yes | Type of third party: `CUSTOMER`, `EMPLOYEE`, `SUPPLIER` |
| exerciseId | UUID | Yes | Unique identifier of the fiscal exercise |
| startDate | DateTime | Yes | Start date of the report (date only, time ignored) |
| endDate | DateTime | Yes | End date of the report (date only, time ignored) |
| isWithoutZeroBalance | Boolean | No | Exclude third parties with zero balance |
| isBalanceComparative | Boolean | No | Compare with previous balances |
| showTemporaryCarryForward | Boolean | No | Include temporary carryforwards |

### accountingThirdPartyBalance Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| thirdPartyId | UUID | — | Unique identifier of the third party |
| thirdPartyDescription | String | — | Description of the third party (max 100) |
| accumulatedBalance | Decimal | — | Accumulated balance of the third party account (only if `isBalanceComparative` is true) |
| accumulatedCreditAmount | Decimal | — | Credit amount for the entire fiscal exercise (only if `isBalanceComparative` is true) |
| accumulatedCreditBalance | Decimal | — | Signed credit amount for the entire fiscal exercise (only if `isBalanceComparative` is true) |
| accumulatedDebitAmount | Decimal | — | Accumulated debit amount for the entire fiscal exercise (only if `isBalanceComparative` is true) |
| accumulatedDebitBalance | Decimal | — | Signed accumulated debit balance for the entire fiscal exercise (only if `isBalanceComparative` is true) |
| balance | Decimal | — | Net balance of the third party account |
| creditAmount | Decimal | — | Total credit amount within the specified period |
| creditBalance | Decimal | — | Signed credit balance within the specified period |
| debitAmount | Decimal | — | Total debit amount within the specified period |
| debitBalance | Decimal | — | Signed debit balance within the specified period |

---

## accountingBalanceSheet

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Read | Action (Query) | accountingBalanceSheet | AccountingBalanceSheetGLDtoInput |

### Description

The AccountingBalanceSheet service provides a balance sheet report for accounting. This service generates a structured view of assets and liabilities, allowing a clear snapshot of the financial position over a specified period.

### Functionality

- **Balance Sheet Generation:** Returns the balance sheet structured by lines and sections.
- **Fiscal Period Selection:** Generates the balance sheet for a specific date range.
- **Line Filtering:** Allows excluding lines without amounts.
- **Carryforward Handling:** Option to include or exclude temporary carryforwards.

### Input Parameters

- **Report Dates:** Mandatory, defines the reporting period using `StartDate` and `EndDate`.
- **Display Options:** Optional parameters to control the output:
  - `OnlyLinesWithAmount`: Whether to include only lines that contain amounts.
  - `ShowTemporaryCarryForward`: Whether to include temporary carryforward lines.

### Response

The service returns an array of `AccountingBalanceSheetOutputGLDto` objects, each representing a line of the balance sheet:

- **Structure Information:** `Code`, `Description`, `LineType`, `Order`
- **Amounts:** `AmountColumn1`, `AmountColumn2`, `AmountTotal`
- **Layout Information:** `MultiColumn` — Indicates whether the line uses multiple amount columns

> **Tips and Tricks:** This query returns the full balance sheet structure in a single call. Pagination is not required, as the result is a complete ordered array representing the balance sheet layout.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### GraphQL Query

```graphql
query ($input: AccountingBalanceSheetGLDtoInput!) {
  accountingBalanceSheet(input: $input) {
    code
    description
    lineType
    order
    multiColumn
    amountColumn1
    amountColumn2
    amountTotal
  }
}
```

### GraphQL Variables

```json
{
  "input": {
    "startDate": "2026-01-01",
    "endDate": "2026-12-31",
    "onlyLinesWithAmount": false,
    "showTemporaryCarryForward": false
  }
}
```

### Example Response

```json
{
  "data": {
    "accountingBalanceSheet": [
      {
        "amountColumn1": 0,
        "amountColumn2": 0,
        "amountTotal": 0,
        "multiColumn": false,
        "code": "A0",
        "description": "ACTIF",
        "lineType": "ACCOUNT_CALCULATION",
        "order": 1
      },
      {
        "amountColumn1": 0,
        "amountColumn2": 0,
        "amountTotal": 0,
        "multiColumn": false,
        "code": "A000",
        "description": " CAPITAL SOUSCRIT NON-APPELÉ (I)",
        "lineType": "RESULT_CALCULATION",
        "order": 2
      }
    ]
  }
}
```

### accountingBalanceSheet Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| startDate | DateTime | Yes | Start date of the report (date only, time ignored) |
| endDate | DateTime | Yes | End date of the report (date only, time ignored) |
| onlyLinesWithAmount | Boolean | No | true = only lines with non-zero amounts; false = full structure |
| showTemporaryCarryForward | Boolean | No | Include temporary carryforward lines |

### accountingBalanceSheet Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| code | String | — | Balance sheet line code (max 10) |
| description | String | — | Description of the balance sheet line (max 255) |
| lineType | Enum | — | Type of line: `UNDEFINED`, `ACCOUNT_CALCULATION`, `LABEL`, `LINE_CALCULATION`, `RESULT_CALCULATION`, `TITLE` |
| order | Int | — | Display order of the line |
| multiColumn | Boolean | — | true = line is split across multiple columns; false = single total amount |
| amountColumn1 | Decimal | — | Amount for the first column |
| amountColumn2 | Decimal | — | Amount for the second column |
| amountTotal | Decimal | — | Total amount of the line |

---

## accountingProfitAndLoss

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Read | Action (Query) | accountingProfitAndLoss | AccountingProfitAndLossGLDtoInput |

### Description

The AccountingProfitAndLoss service provides a profit and loss report for accounting. This service generates a structured view of revenues and expenses, allowing analysis of financial performance over a specified period.

### Functionality

- **Profit and Loss Generation:** Returns a profit and loss statement structured by lines and sections.
- **Fiscal Period Selection:** Generates the report for a specific date range.
- **Line Filtering:** Allows excluding lines without amounts.
- **Multi-column Support:** Supports single or multiple amount columns depending on configuration.

### Input Parameters

- **Report Dates:** Mandatory, defines the reporting period using `StartDate` and `EndDate`.
- **Display Options:**
  - `OnlyLinesWithAmount`: Whether to include only lines with non-zero amounts.

### Response

The service returns an array of `AccountingProfitAndLossOutputGLDto` objects, each representing a line of the profit and loss statement:

- **Structure Information:** `Code`, `Description`, `LineType`, `Order`
- **Amounts:** `AmountColumn1`, `AmountColumn2`, `AmountTotal`
- **Layout Information:** `MultiColumn` — Indicates whether the line uses multiple amount columns

> **Tips and Tricks:** This query returns the full profit and loss structure in a single call. Pagination is not required, as the result is a complete ordered array.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### GraphQL Query

```graphql
query ($input: AccountingProfitAndLossGLDtoInput!) {
  accountingProfitAndLoss(input: $input) {
    code
    description
    lineType
    order
    multiColumn
    amountColumn1
    amountColumn2
    amountTotal
  }
}
```

### GraphQL Variables

```json
{
  "input": {
    "startDate": "2025-01-01",
    "endDate": "2025-12-31",
    "onlyLinesWithAmount": false
  }
}
```

### Example Response

```json
{
  "data": {
    "accountingProfitAndLoss": [
      {
        "amountColumn1": 0,
        "amountColumn2": 0,
        "amountTotal": 0,
        "multiColumn": false,
        "code": "P0",
        "description": "PRODUITS D'EXPLOITATION",
        "lineType": "1",
        "order": 1
      },
      {
        "amountColumn1": 16640687.72,
        "amountColumn2": 0,
        "amountTotal": 16640687.72,
        "multiColumn": false,
        "code": "P001",
        "description": "MONTANT NET DU CHIFFRE D'AFFAIRES",
        "lineType": "4",
        "order": 2
      }
    ]
  }
}
```

### accountingProfitAndLoss Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| startDate | DateTime | Yes | Start date of the report (date only, time ignored) |
| endDate | DateTime | Yes | End date of the report (date only, time ignored) |
| onlyLinesWithAmount | Boolean | No | true = only lines with non-zero amounts; false = full structure |

### accountingProfitAndLoss Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| code | String | — | Profit and loss line code (max 10) |
| description | String | — | Description of the profit and loss line (max 255) |
| lineType | Enum | — | Type of line: `UNDEFINED`, `ACCOUNT_CALCULATION`, `LABEL`, `LINE_CALCULATION`, `RESULT_CALCULATION`, `TITLE` |
| order | Int | — | Display order of the line |
| multiColumn | Boolean | — | true = line is split across multiple columns; false = single total amount |
| amountColumn1 | Decimal | — | Amount for the first column |
| amountColumn2 | Decimal | — | Amount for the second column |
| amountTotal | Decimal | — | Total amount of the line |

---

## accountingReportPositions

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Read | Action (Query) | accountingReportPositions | AccountingReportPositionGLDtoInput |

### Description

The AccountingReportPositions service retrieves the location of accounts in the balance sheet or Profit & Loss report.

### Functionality

- **Retrieve Account Positions:** Returns the location of accounts in the balance sheet or Profit & Loss report.
- **Legislation-Based Categorization:** Account positioning depends on the applicable legislation and accounting model.
- **Balance Type Consideration:** Only accounts classified as Balance Sheet (1) or Profit & Loss (2) are included.
- **Detailed or Summary View:** Allows filtering results to include only summary lines or detailed lines.
- **Account Filtering:** Can return all accounts or a specific account based on `accountId`.
- **Multiple Position Handling:** Identifies the main position of an account, supports alternative positions, and excludes details by default unless requested.
- **Undefined Accounts:** If an account is not found in the balance guide, it is labeled as "undefined".

### Input Parameters

- **Account Identifier** (`accountId`, Optional): If provided, returns the positions for a specific account; otherwise, retrieves all accounts.
- **Detail Filter** (`isDetail`, Boolean, Optional, Default: `false`):
  - `false`: Returns only main balance guide positions.
  - `true`: Includes detailed positions where applicable.

### Response

The service returns an array of `AccountingReportPositionOutputGLDto` objects, each representing an account with the following fields:

- **Account Details:**
  - `id`: Account id
  - `code`: Account code
  - `description`: Account description
  - `name`: Account name
  - `accountType`: Type of the account (e.g., Balance Sheet, Profit & Loss)
  - `subAccountType`: Subcategory of the account
  - `accountLevel`: Account hierarchy level
- **Additional Properties:**
  - `deactivated`: Whether the account is deactivated
  - `requestedAction`: Indicates if any action is required on the account
  - `taxTreatmentId`: ID of the applicable tax treatment
  - `defaultType`: Default type of the account
  - `datevAutomaticAccount`: Whether the account is automatically managed in DATEV
  - `datevAccountNumber`: The account number used in the DATEV system
  - `collectiveAccount`: Whether the account is collective
  - `applyVat`: Whether VAT should be applied to the account
- **Balance Position Information** (`reportPositions`):
  - `balanceGuideLineId`: Unique identifier of the balance guide line
  - `balanceGuideLineCode`: Code representing the balance guide line
  - `balanceGuideLineDescription`: Description of the balance guide line
  - `isDetail`: Indicates if the line is a detailed breakdown (true = detailed)
  - `isMain`: Indicates if this is the main position of the account (true = main position)
  - `alternativePositionIfNegative`: Indicates if the account has an alternative position when its balance is negative

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### Example GraphQL Query

> **Note:** In this example, an account ID is provided, so the request retrieves the positions for this specific account. If no ID were mentioned, the response would include positions for all accounts. `isDetail = true` means that if the account has multiple positions, all details for each position are included. If `isDetail = false`, only the first position would be returned.

```graphql
query($input: AccountingReportPositionGLDtoInput!) {
  accountingReportPositions(input: $input) {
    id
    code
    description
    accountType
    accountLevel
    subAccountType
    taxTreatmentId
    deactivated
    collectiveAccount
    datevAutomaticAccount
    datevAccountNumber
    applyVat
    reportPositions {
      balanceGuideLineId
      balanceGuideLineCode
      balanceGuideLineDescription
      isDetail
      isMain
      alternativePositionIfNegative
    }
  }
}
```

### Example GraphQL Variables

```json
{
  "input": {
    "accountId": "57dc1e99-652e-48ca-b1ef-2e5b233e988d",
    "isDetail": true
  }
}
```

### Example Response

```json
{
  "data": {
    "accountingReportPositions": [
      {
        "id": "3cff68b5-e785-4b2b-95a6-1fae95af9f2c",
        "code": "612200",
        "description": "612200 - Redevance crédit-bail mobilier",
        "accountType": "BALANCE_SHEET",
        "accountLevel": "SUB_ACCOUNT",
        "subAccountType": "CHARGE",
        "taxTreatmentId": "40904303-5c02-4784-a505-9c0325b7a40f",
        "deactivated": false,
        "collectiveAccount": false,
        "datevAutomaticAccount": false,
        "datevAccountNumber": null,
        "applyVat": false,
        "reportPositions": [
          {
            "balanceGuideLineId": "e3e4fc20-bcb4-419c-bb09-b4ee39e61dac",
            "balanceGuideLineCode": "C00203",
            "balanceGuideLineDescription": "Autres achats et charges externes (1)",
            "isDetail": false,
            "isMain": false,
            "alternativePositionIfNegative": false
          },
          {
            "balanceGuideLineId": "ae51dc12-f108-41a6-9583-6b45b6979c40",
            "balanceGuideLineCode": "C0020301",
            "balanceGuideLineDescription": "Redevances de crédit-bail mobilier",
            "isDetail": true,
            "isMain": false,
            "alternativePositionIfNegative": false
          }
        ]
      }
    ]
  }
}
```

### accountingReportPositions Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| accountId | UUID | No | ID of the specific account to retrieve |
| isDetail | Boolean | No (Default: false) | Whether to include detailed positions |

### accountingReportPositions Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | — | Account ID (system field) |
| code | String | — | Account code |
| description | String | — | Account description |
| name | String | — | Account name |
| accountLevel | Enum | — | Account hierarchy level: `UNDEFINED`, `ACCOUNT`, `BREAKDOWN`, `GROUP`, `SUB_ACCOUNT`, `SUB_GROUP`, `TOTAL` |
| accountType | Enum | — | Account type: `UNDEFINED`, `BALANCE_SHEET`, `ECPN`, `INCOME_STATEMENT`, `SPECIAL`, `STATISTICAL` |
| subAccountType | Enum | — | Subcategory of the account: `UNDEFINED`, `AGGREGATE_CARRIED_FORWARD`, `BALANCE_CARRIED_FORWARD`, `BANK`, `BORROWED_CAPITAL`, `BORROWED_CAPITAL_OUTPUT_TAX`, `CAPITAL`, `CASH`, `CHARGE`, `COST`, `CURRENT_ASSETS`, `CURRENT_ASSETS_INPUT_TAX`, `CUSTOMER`, `CUSTOMER_PREPAYMENT`, `CUSTOMER_PREPAYMENT_TAX_INCLUDED`, `CUSTOMER_RETURNABLE_PACKAGING`, `DEPRECIATION_PROVISION`, `EMPLOYEE`, `EXCHANGE_RATE_EXPENSE`, `EXCHANGE_RATE_INCOME`, `FIXED_ASSET`, `INPUT_TAX`, `MANAGEMENT_RESULT`, `NEUTRAL_EXPENSES`, `NEUTRAL_REVENUE`, `OTHER_BALANCE_SHEET_ACCOUNT`, `OUTPUT_TAX`, `PROVIDER`, `REDUCTION_IN_EXPENSES`, `RESULT_BALANCE_SHEET`, `REVENUE`, `SALES_DEDUCTION`, `STATISTICAL_BALANCE`, `STATISTICAL_CONSUMPTION`, `STOCK`, `SUPPLIER_PREPAYMENT`, `SUPPLIER_PREPAYMENT_TAX_INCLUDED`, `SUPPLIER_RETURNABLE_PACKAGING`, `TITLE` |
| taxTreatmentId | UUID | — | ID of the applicable tax treatment |
| deactivated | Boolean | — | Whether the account is deactivated |
| collectiveAccount | Boolean | — | Whether the account is collective |
| datevAutomaticAccount | Boolean | — | Whether the account is automatically managed in DATEV |
| datevAccountNumber | String | — | The account number used in the DATEV system |
| applyVat | Boolean | — | Whether VAT should be applied to the account |
| reportPositions | Array | — | List of positions where the account appears |

### accountingReportPositions / reportPositions Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| balanceGuideLineId | UUID | — | Unique identifier of the balance guide line |
| balanceGuideLineCode | String | — | Code representing the balance guide line |
| balanceGuideLineDescription | String | — | Description of the balance guide line |
| isDetail | Boolean | — | Whether this is a detailed breakdown (true) |
| isMain | Boolean | — | Whether this is the main position (true = main position) |
| alternativePositionIfNegative | Boolean | — | If true, the account has a different position when the balance is negative |

---

## accountingKpis

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Read | Action (Query) | accountingKpis | AccountingKpiInput |

### Description

The Accounting KPIs service exposes a set of accounting indicators (Key Performance Indicators) that can be retrieved for a given organization. Each KPI corresponds to a standardized calculation on accounting balances, adapted per legislation (FR, ES, or DE).

- Provides accounting ratios and indicators such as turnover, purchases, trade margin, equity, etc.
- Adapts the calculation logic to the organization's legislation.
- Supports retrieval of one or multiple KPIs in a single call.

### Input Parameters

- `kpiTypes` -- Mandatory. One or several values from the enumeration `AccountingIndicatorType` (see Available KPIs table below).

### Response

The service returns a structured list of KPIs. Each KPI contains:

- `type` -- The KPI identifier (enum).
- `value` -- The numeric value of the KPI.
- `currency` -- The currency code (e.g. EUR).

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### GraphQL Query

```graphql
query ($kpis: [AccountingIndicatorType!]!) {
  accountingKpis(input: { kpiTypes: $kpis }) {
    type
    value
    currency
  }
}
```

### Variable for a Single KPI

```json
{
  "kpis": ["RESULT"]
}
```

### Response for a Single KPI

```json
{
  "data": {
    "accountingKpis": [
      {
        "type": "RESULT",
        "value": 7591496.65,
        "currency": "EUR"
      }
    ]
  }
}
```

### Variable for Multiple KPIs

```json
{
  "kpis": ["RESULT", "FINANCIAL_RESULT", "EXCEPTIONAL_RESULT"]
}
```

### Response for Multiple KPIs

```json
{
  "data": {
    "accountingKpis": [
      {
        "type": "RESULT",
        "value": 7591496.65,
        "currency": "EUR"
      },
      {
        "type": "FINANCIAL_RESULT",
        "value": 0.00,
        "currency": "EUR"
      },
      {
        "type": "EXCEPTIONAL_RESULT",
        "value": -138.88,
        "currency": "EUR"
      }
    ]
  }
}
```

### Available KPIs

| # | KPI | Description |
|---|-----|-------------|
| 1 | ASSETS | Total assets |
| 2 | EQUITY | Equity |
| 3 | EXCEPTIONAL_RESULT | Exceptional result |
| 4 | FINANCIAL_RESULT | Accounting result (interests, accounting income/expenses) |
| 5 | NET_RESULT | Net result |
| 6 | OTHER_EXPENSES | Other operating expenses |
| 7 | PURCHASES | Purchases (goods & raw materials) |
| 8 | RESULT | Operating result (profit/loss) |
| 9 | STAFF_COST | Staff costs (wages, social charges) |
| 10 | TAXES | Taxes and duties |
| 11 | TRADE_MARGIN | Trade margin (turnover - purchases) |
| 12 | TREASURY_BALANCE | Treasury balance (cash position) |
| 13 | TURNOVER | Turnover (Sales revenue) |
| 14 | WORKING_CAPITAL | Working capital |

### Example Response for All KPIs

```json
[
  { "type": "ASSETS", "value": 10481868.79, "currency": "EUR" },
  { "type": "EQUITY", "value": 7591496.65, "currency": "EUR" },
  { "type": "EXCEPTIONAL_RESULT", "value": -138.88, "currency": "EUR" },
  { "type": "FINANCIAL_RESULT", "value": 0, "currency": "EUR" },
  { "type": "NET_RESULT", "value": 7591357.77, "currency": "EUR" },
  { "type": "OTHER_EXPENSES", "value": 18000, "currency": "EUR" },
  { "type": "PURCHASES", "value": 4612065.98, "currency": "EUR" },
  { "type": "RESULT", "value": 7591496.65, "currency": "EUR" },
  { "type": "STAFF_COST", "value": 36836, "currency": "EUR" },
  { "type": "TAXES", "value": 25000.00, "currency": "EUR" },
  { "type": "TRADE_MARGIN", "value": 7805590.65, "currency": "EUR" },
  { "type": "TREASURY_BALANCE", "value": 95267.84, "currency": "EUR" },
  { "type": "TURNOVER", "value": 12417656.63, "currency": "EUR" },
  { "type": "WORKING_CAPITAL", "value": 7735825.94, "currency": "EUR" }
]
```

### accountingKpis Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| kpiTypes | Enum[] | Yes | One or more KPI types: `ASSETS`, `EQUITY`, `EXCEPTIONAL_RESULT`, `FINANCIAL_RESULT`, `NET_RESULT`, `OTHER_EXPENSES`, `PURCHASES`, `RESULT`, `STAFF_COST`, `TAXES`, `TRADE_MARGIN`, `TREASURY_BALANCE`, `TURNOVER`, `WORKING_CAPITAL` |

### accountingKpis Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| type | Enum | — | KPI type (AccountingIndicatorType) |
| value | Decimal | — | Numeric value |
| currency | String | — | Currency code (e.g. EUR). May be null if N/A |
