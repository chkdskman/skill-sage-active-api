# Accounting Entries — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/accountingentries
> - https://developer.sage.com/sageactive/resources/createaccountingentryusingcodes
> - https://developer.sage.com/sageactive/resources/createaccountingentryusingids
> - https://developer.sage.com/sageactive/resources/accountingentrysession

## HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | (see createAccountingEntryUsingCodes / createAccountingEntryUsingIds) | |
| POST | Delete | Mutation | deleteAccountingEntry | AccountingEntryDeleteGLDtoInput |
| POST | Read | Query | accountingEntries filtered by id | |
| POST | List | Query | accountingEntries | |
| POST | List | Query | accountingEntryLines | |

## Description

Accounting entries, known as AccountingEntry in the API context and Journal entries in the product interface, in the context of financial management and record-keeping, refer to the detailed transactions that accurately record the movement of funds, assets, liabilities, and equity within an organization. These entries serve as crucial components of a company's financial records, capturing various financial activities such as revenue generation, expense allocation, and asset acquisitions. Accounting entries are categorized and organized into journals based on their specific characteristics, and these characteristics are determined by the associated journal. It's important to note that an accounting entry must be balanced, ensuring that debits equal credits. This means that accounting entries are complete accounting documents rather than individual lines of entries.

## Required Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | *(Deprecated)* Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

## Quick Tips

- It is possible to query either accountingEntry, accountingEntryLines, accountingEntryPayments, or directly from accountingEntryDimensionTags.
- Querying accountingEntries allows you to retrieve entire entries with all their associated line items. This is useful when you need the complete context of an entry, including all details of the related lines.
- Querying accountingEntryLines directly is beneficial when you want to filter or aggregate specific line item data across multiple entries.
- Querying accountingEntryPayments directly gives you access to payment reconciliation information. In this case, the accountingEntryLine dataloader can be used to retrieve details of the corresponding entry line.
- Querying accountingEntryDimensionTags gives you access to analytic allocations applied to each entry line. You can use the accountingEntryLine dataloader to retrieve the related line for each analytic tag.

## accountingEntries Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| sourceType | Enum: SALES, ACCOUNTING, BANKING, IMPORT_FILE, YEAR_END_PROCESS, PUBLIC_API, YEAR_END_PROCESS_PREVIEW, SAGE_FR_PAYROLL_CLOUD, SAGE_DE_PAYROLL_CLOUD, FIXED_ASSETS, ONLINE_PAYMENTS | System | Indicates the origin of the accounting entry |
| date | DateTime | | Date (only the date value is considered, time is ignored) |
| documentDate | DateTime | | Document Date (only the date value is considered, time is ignored) |
| accountingExercise | AccountingExercise | | DATALOADER - Fields of AccountingExercise |
| accountingExerciseId | UUID | | Id of AccountingExercise |
| accountingPeriod | AccountingPeriod | | DATALOADER - Fields of AccountingPeriod |
| accountingPeriodId | UUID | | Id of AccountingPeriod |
| journalType | JournalType | | DATALOADER - Fields of JournalType |
| journalTypeId | UUID | | JournalType Id |
| session | Session | | DATALOADER - Fields of Session |
| sessionId | UUID | Default auto-calculated | Session id |
| currency | Currency | | DATALOADER - Fields of Currency |
| currencyId | UUID | Default auto-calculated | Currency ID |
| entryType | Enum: UNDEFINED, CASH_VAT_ADJUSTMENT, CLOSURE_ACCOUNTING, CLOSURE_EXERCISE, CLOSURE_WORTH, GENERAL, OPENING, TEMPORARY_CARRY_FORWARD | | Type of entry |
| number | Int | | Number (operational counter id) |
| documentNumber | String(50) | | Document number or reference of the entry. Mandatory in DE legislation. For purchases or sales, it can be filled with the invoice number. |
| tanNumber | Int | | Counter that automatically increases by 1 (DE Only) |
| description | String(200) | | Description. Optional value, used as template for lines from the interface to assign default value to lines. |
| closeDate | DateTime | | Closing date of entry (not visible in UI, date only) |
| isClosed | Boolean | | True if closed |
| accountingEntryLines[] | Array | | List of all lines of entry |
| salesInvoice | SalesInvoiceHeader | | DATALOADER - Sales Invoice Header Fields. Only relevant when the entry was generated from a posted invoice in the Sales module. |
| sourceEntityId | UUID | | ID of a sales invoice, purchase invoice, collected payment, or issued payment (can be null). If provided, see sourceType to identify the entity. salesInvoiceId holds the same value when sourceType=SALES. |

### Info: sourceType

Indicates the origin of the accounting entry, detailing how it was generated. Each source type corresponds to a specific method or area within the business operations that has contributed to the accounting record.

### Info: sessionId and session

A session adds identifiable information to journal entries.

- **sessionId**: The session identifier, this value is mandatory.
- **session** allows retrieving:
  - **id**: The session identifier
  - **name**: The session name (e.g., Automatisch erstellt)
  - **stamp**: The session name and timestamp (e.g., Automatisch erstellt 12.06.2024 10:20:04)
  - **deactivated**: The session status. When true, the session cannot be used for a new accounting entry.

### Info: accountingEntryLines

List of lines in the accounting entry. The accounting entry must be balanced -- the total of the lines with a debit must be equal to the total of the lines with a credit. Adding an unbalanced entry will return an error.

## accountingEntryLines Fields

If the query targets accountingEntries, then use `accountingEntries/accountingEntryLines` to get the details of the lines. If the query targets accountingEntryLines, access the following fields directly, including the option to retrieve information on the parent accountingEntries.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| accountingEntry | AccountingEntry | | DATALOADER - Fields of the parent accountingEntries |
| accountingEntryId | UUID | | accountingEntry ID |
| subAccount | AccountingAccount | | DATALOADER - Fields of AccountingAccount |
| subAccountId | UUID | | Id of accountingAccount |
| order | Int | | Classification of lines from 0 to n |
| documentNumber | String(50) | | Document number or reference of the entry |
| contraAccount | String(113) | | Contra Account (DE only) |
| description | String(200) | | Description |
| debitAmount | Decimal | | Amount debit |
| creditAmount | Decimal | | Amount credit |
| accountingEntryThirdParty | ObjectType | | ThirdParty. Lines with a third party CUSTOMER, SUPPLIER, EMPLOYEE. |
| accountingEntryPayment | ObjectType | | Matching |
| accountingEntryDimensionTags[] | Array | | Analytics allocation of dimensions and tags |
| accountingEntryInvoice | ObjectType | | Invoice. Lines with a third party CUSTOMER, SUPPLIER for journal types PURCHASE_INVOICE or SALES_INVOICE. |

## accountingEntryThirdParty Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| thirdPartyId | UUID | | Third party id. If journal type = PURCHASE_INVOICE the third party is a Supplier. If journal type = SALES_INVOICE the third party is a Customer. In other cases, it can be Supplier, Customer or Employee. |
| socialName | String(70) | | Third Name. Found in the third party as socialName for customer & supplier and name for employee. |
| code | String(17) | | Code. Found in the third party as code. |
| origin | Enum: CUSTOMER, EMPLOYEE, SUPPLIER | | Type of third |
| identificationTypeId | UUID | | DocumentType Id. Found in the third party as documentTypeId. |
| documentId | String(25) | | Document Id. Found in the third party as documentId. |
| vatNumber | String(25) | | VAT Number. Found in the third party as vatNumber. |
| countryAcronym | String(2) | | Country Acronym |
| viesCode | String | Read-only | VIES value prefix of the European Community VAT numbers |

## accountingEntryPayment Fields

This section provides information about accounting reconciliation (also known as "matching") of an entry line. Reconciliation links different accounting entries that offset each other, such as an invoice and its payment. These details are available in read-only mode. You cannot create or modify them directly via the API.

You can also query accountingEntryPayments directly. In this case, the accountingEntryLine dataloader allows you to retrieve the details of the related entry line.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| accountingEntryLine | AccountingEntryLine | | DATALOADER - Fields of AccountingEntryLine |
| accountingEntryLineId | UUID | | Id of the accounting entry line |
| matching | Enum: FULL_MATCH, PARTIAL_MATCH | | Status matching. PARTIAL_MATCH means partially reconciled, FULL_MATCH indicates complete reconciliation. |
| matchingLetter | String | | Letter matching. Lowercase letters for partial matches (a, b, c...), uppercase for full matches (A, B, C...). |
| matchId | UUID | | Id of the match. All lines matched together share the same matchId, regardless of the matching letter used. |

## accountingEntryDimensionTags Fields

An analytic dimension represents a customizable axis used to categorize accounting entry lines for analytical purposes. Typical examples include dimensions such as Warehouse, Department, or Project.

Each dimension is configured with a list of tags, which define the allowed values for that dimension. For example, the Warehouse dimension may include tags such as Lyon, Toulouse, and Rouen, each representing a specific location used in allocation.

When creating accounting entries, you can allocate each line across one or more analytic dimensions by assigning a specific tag from the list defined for each dimension.

You can also configure accounts in the chart of accounts to assign default dimension tags, which will be automatically applied to entry lines using those accounts. See API resources overview / Accounting Accounts / AccountingAccountDimensionTag.

You can also query accountingEntryDimensionTags directly. In this case, the accountingEntryLine dataloader allows you to retrieve the details of the related entry line.

### Example: Analytic Allocation on Accounting Entry Lines

The list of available dimensions and their tags is defined in your setup:

- **Dimension 1: Warehouse** -- Available Tags: Toulouse, Lyon, Rouen
- **Dimension 2: Department** -- Available Tags: Sales, Logistics, R&D

Suppose you allocate an entry line to Warehouse=Toulouse and Department=Sales. The full amount of the accounting entry line is allocated to both tags.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| accountingEntryLine | AccountingEntryLine | | DATALOADER - Fields of AccountingEntryLine |
| accountingEntryLineId | UUID | | Id of the accounting entry line |
| dimensionId | UUID | | Id of the dimension (e.g. Warehouse). Refers to the analytic category. |
| dimensionTagId | UUID | | Id of the selected tag. Refers to a tag that belongs to the selected dimension. |
| amount | Decimal | | Allocated amount on this dimension. Specifies how much of the accounting entry amount is allocated to this tag. |

> **Warning:** Analytic dimension and tag information is currently read-only. You cannot assign or modify these values through the API at this stage. Dataloaders are not currently available for analytic dimensions and dimensionTags. If you need to retrieve additional details (e.g., dimension name for a given dimensionId), you must manually correlate the values using the identifiers returned by the API.

## accountingEntryInvoice Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| documentDate | DateTime | | Date of the invoice |
| operationDate | DateTime | | Fulfilment date of the invoice |
| externalInvoiceNumber | String(50) | | Invoice number |
| isCashVat | Boolean | | True if Cash VAT (FR and DE only) |
| amount | Decimal | | Amount of the invoice |
| openItems[] | Array | | Open items |
| accountingEntryTaxes[] | Array | | Tax registers |

### Corrective Invoices (Spanish Legislation Only)

These fields are only available in Sage Active ES within the context of corrective invoices. Corrective invoices align with the Spanish VAT regulation (Reglamento de Facturacion - Real Decreto 1619/2012), which distinguishes between two types of corrective invoices (facturas rectificativas) based on whether they adjust the original invoice or fully replace it.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| creditNoteType | Enum: UNDEFINED, BY_DIFFERENCES, SUBSTITUTIVE | | Specifies the category of the corrective invoice according to Spanish VAT regulations |
| creditNoteReason | Enum: UNDEFINED, R1, R2, R3, R4, R5 | | Specifies the reason for issuing the corrective invoice |
| originalEntryInvoiceTaxesBaseSum | Decimal | | Total taxable base amount of the original invoice being corrected |
| originalEntryInvoiceTaxesSurchargeAmountSum | Decimal | | Total surcharge amount applied to the original invoice |
| originalEntryInvoiceTaxesTaxAmountSum | Decimal | | Total tax amount of the original invoice being corrected |
| originalAccountingEntryInvoice | AccountingEntryInvoice | | DATALOADER - Provides access to the accountingEntryInvoice of the original invoice |
| originalAccountingEntryInvoiceId | UUID | | ID of the original invoice |

**creditNoteType values:**

- **BY_DIFFERENCES** ("Rectificativa por diferencias"): Used when the corrective invoice partially adjusts the original invoice (e.g., correcting price differences or quantity errors). The original invoice remains valid, but its amounts are adjusted. Common cases include VAT miscalculations, incorrect unit prices, or missing line items.
- **SUBSTITUTIVE** ("Rectificativa sustitutiva"): Used when the original invoice is entirely replaced by the corrective invoice. The original invoice is considered null and void. Often used when an invoice was issued incorrectly (e.g., wrong client details, incorrect tax rates).
- **UNDEFINED**: No specific classification; used as a fallback.

**creditNoteReason values:**

- **R1**: Return of packaging
- **R2**: Bankruptcy proceedings
- **R3**: Bad debt -- used when the original invoice cannot be collected due to non-payment, allowing VAT recovery
- **R4**: Other corrective invoices -- covers general corrections not included in other categories
- **R5**: Simplified invoice correction

## accountingEntryInvoice/openItems Fields

An Open Item is a financial transaction that allows you to know if it has been fully settled or paid. Each open item includes detailed information such as the total amount, accumulated amount paid, and the payment due date. For a single invoice, there can be as many open items as there are different payment installments, which is why openItems is structured as an array. The payment status provides insight into the progress of settling each item. Additionally, the identifier of the payment method used is provided to track the settlement method.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| amount | Decimal | | Total amount of the item |
| paidAmountAccumulated | Decimal | | Accumulated amount that has been paid |
| dueDate | DateTime | | Due date for the payment (date only, time ignored) |
| status | Enum: NOT_SPECIFIED, COLLECTED, PAID, PARTIAL | | Status of the payment |
| paymentMean | PaymentMean | | DATALOADER - Fields of PaymentMean |
| paymentMeanId | UUID | | Identifier of the payment method used |

**status values:**

- **NOT_SPECIFIED**: No specific payment status has been detailed
- **COLLECTED**: Payment has been fully collected from customers
- **PAID**: Payment has been fully made to suppliers
- **PARTIAL**: Payment for the item has been made partially

## accountingEntryInvoice/accountingEntryTaxes Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| taxTreatmentId | UUID | | Id of the tax Treatment (see Taxes Treatments) |
| taxGroupId | UUID | | Id of the Tax Group of tax treatment (found in Taxes Treatments) |
| taxType | Enum: NOT_SPECIFIED, DOM, FOREIGN, IGIC, IPSI, IRPF, IVA, IVA_PT, TVA, UST | | Tax Type of the tax treatment (found in Taxes Treatments) |
| taxId | UUID | | Id of the tax |
| taxPercentage | Decimal | | Percentage of the tax (found in tax by groupId=taxGroupId) |
| taxBase | Decimal | | Base of tax calculation |
| taxAmount | Decimal | | Tax amount |
| deductible | Boolean | | true for a deductible tax, false for a collected tax |
| regimeType | Enum: GENERAL | | Always fill in GENERAL |
| hasEquivalenceSurcharge | Boolean | | Indicates whether the equivalence surcharge is applied (Spain only) |
| equivalenceSurchargePercentage | Decimal | | Percentage applied for equivalence surcharge (Spain only) |
| equivalenceSurchargeQuota | Decimal | | Fixed amount of the equivalence surcharge quota (Spain only) |

### Equivalence Surcharge Fields (Spain Only)

These fields relate to the equivalence surcharge (Recargo de Equivalencia), a special VAT regime applicable to specific Spanish retailers.

- **hasEquivalenceSurcharge**: Indicates whether the surcharge applies to the invoice
- **equivalenceSurchargePercentage**: The percentage applied when the surcharge is enabled, depending on product type
- **equivalenceSurchargeQuota**: An optional fixed amount used instead of a percentage, in specific tax contexts

---

## Create Accounting Entry Using Codes

### HTTP Operation

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | createAccountingEntryUsingCodes | AccountingEntryCreateUsingCodesGLDtoInput |

### Description

Creation of Accounting Entries by using business Codes instead of internal Sage Active ids. This approach can be simpler because developers are likely more familiar with business codes as they directly relate to the business domain. This reduces the need for additional database lookups to translate codes to IDs, which can result in faster performance due to caching.

### Advantages

- Direct use of journal codes, third-party codes, and account codes, eliminating the need for Sage Active IDs.
- Automatic validation of the existence of journal codes, third-party codes, and account codes.
- Automatic detection of the third-party type (Client, Supplier, Employee) based on the sub-account class.
- Optimized performance through the utilization of an internal cache for fetching journal, third-party, and account IDs, thereby reducing the number of queries required.
- Simplified model definition with streamlined lines that eliminate the need to manage accountingEntryThirdParty and accountingEntryInvoice sub-resources.
- Automatic assignment of the description and document number from the header to the lines if not defined in the line.
- Automatic creation of tax registers if the consistency of the transaction amounts allows it.
- Automatic summation of debit or credit from multiple lines of the same third party in the entry, merging them into a single entry line.
- Preservation of all business controls managed by Sage Active to ensure the integrity of the added entries.

To see examples of mutations that include the adding of accounting entries, download the Postman collection: Quick start / 5. Test your first query in Postman.

### Example: Sales Invoice Entry (Using Codes)

This example pertains to a sales invoice. To convert it into a purchase invoice, simply use a purchase journal and a supplier code.

**GraphQL Mutation:**

```graphql
mutation ($values:AccountingEntryCreateUsingCodesGLDtoInput!) {
  createAccountingEntryUsingCodes (input: $values) {
    id
    number
  }
}
```

**GraphQL Variables:**

```json
{
  "values": {
    "description": "Facture FA0022",
    "date": "2023-01-01",
    "documentDate": "2022-12-16",
    "documentNumber": "FA0022",
    "journalTypeCode": "VTE",
    "accountingEntryLines": [
      {
        "thirdCode": "BAGUES",
        "description": "Facture FA0022 regularisation",
        "creditAmount": 0,
        "debitAmount": 158.25,
        "subAccountCode": "411000000"
      },
      {
        "creditAmount": 150,
        "debitAmount": 0,
        "subAccountCode": "701005000"
      },
      {
        "creditAmount": 8.25,
        "debitAmount": 0,
        "subAccountCode": "445710500"
      }
    ]
  }
}
```

**Example Response:**

```json
{
  "data": {
    "createAccountingEntryUsingCodes": {
      "id": "e451aff7-004d-4e72-a6fb-4ea25e9d9c64",
      "number": 785
    }
  }
}
```

### Tax Registers (Automatic Creation)

The creation of tax registers for purchase and sales invoices is now automatic. Registers are created if the prerequisites are met; otherwise, priority is given to the creation of the accounting entry without creating the registers, rather than allowing the Sage Active business controller to reject the entry for non-compliance with the registers.

The conditions are:

- Tax codes for revenue or expense accounts must be defined.
- The VAT accounts defined in the tax codes of these accounts must be those present in the entry.
- The VAT amount and the associated taxable base for the VAT account and the expense or revenue account must be consistent with the VAT rate of the tax code.
- Other management rules not respected or not yet accounted for by Sage Active.

### Required Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | *(Deprecated)* Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### createAccountingEntryUsingCodes Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| date | DateTime | Yes | Date of the accounting entry (date only, time ignored) |
| journalTypeCode | String | Yes | An existing journal code |
| documentDate | DateTime | No | Date of the accounting document. If not provided, automatically set to match the entry date. (date only, time ignored) |
| documentNumber | String | No | Document number or reference of the entry. Mandatory in DE legislation. For purchases or sales, can be filled with the invoice number. |
| description | String | No (FR/ES), Yes (DE) | Description. Used as a template for lines from the interface, assigns default value to lines. |
| accountingEntryLines[] | Array | Yes | List of all entry lines. The entry must be balanced (debits = credits). |

**Info on sessionId**: The accounting session associated with the entry (DE legislation only). This value is mandatory for DE and automatically assigned: if journalId equals defaultPayrollJournalTypeId, then sessionId is set to defaultPayrollSessionId; otherwise it is set to the default value from the list of sessions AccountingEntrySession.

### createAccountingEntryUsingCodes/accountingEntryLines Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| subAccountCode | String | Yes | Code of an existing general account, required on each entry line |
| thirdCode | String | No | Code of an existing third party. Necessary if the entry pertains to a third party. The type (Customer, Supplier, Employee) is auto-detected from the subAccountCode class. |
| description | String | No | Description of the line. If not specified, assigned from the header description. |
| debitAmount | Decimal | Yes | Amount debit. Entry must be balanced (debits = credits). |
| creditAmount | Decimal | Yes | Amount credit. Entry must be balanced (debits = credits). |

> **Note:** For PURCHASE_INVOICE or SALES_INVOICE journals, if several lines contain the same third party, these lines are merged into one with the sum of the debit or credit. This allows compliance with Sage Active while permitting the addition of entries from an import file containing invoices where the due dates are split over several lines.

### createAccountingEntryUsingCodes Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | | Unique identifier of the entry |
| number | Int | | Entry number generated by the app |

---

## Create Accounting Entry Using IDs

### HTTP Operation

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | createAccountingEntryUsingIds | AccountingEntryCreateUsingIdsGLDtoInput |

### Description

This mutation is based on the same logic as createAccountingEntryUsingCodes, but here the business codes of journals, parties, and general accounts are replaced with their internal Sage Active IDs. Depending on your application's structure, it may be simpler to directly use the business values, or conversely, the internal IDs. Using IDs provides more flexibility and accuracy in complex scenarios where unique identification is crucial, even if it requires additional steps to fetch the IDs beforehand. The choice between using business codes and IDs depends on your specific needs and the complexity of the accounting entry being created.

### Advantages

- Simplified model definition with streamlined lines that eliminate the need to manage accountingEntryThirdParty and accountingEntryInvoice sub-resources.
- Automatic assignment of the description and document number from the header to the lines if not defined in the line.
- Automatic creation of tax registers if the consistency of the transaction amounts allows it.
- Automatic summation of debit or credit from multiple lines of the same third party in the entry, merging them into a single entry line.
- Preservation of all business controls managed by Sage Active to ensure the integrity of the added entries.

To see examples of mutations that include the adding of accounting entries, download the Postman collection: Quick start / 5. Test your first query in Postman.

### Example: Sales Invoice Entry (Using IDs)

This example pertains to a sales invoice. To convert it into a purchase invoice, simply use a purchase journal and a supplier code.

**GraphQL Mutation:**

```graphql
mutation ($values:AccountingEntryCreateUsingIdsGLDtoInput!) {
  createAccountingEntryUsingIds (input: $values) {
    id
    number
  }
}
```

**GraphQL Variables:**

```json
{
  "values": {
    "description": "Facture FA0022",
    "date": "2023-01-01",
    "documentDate": "2022-12-16",
    "documentNumber": "FA0022",
    "journalTypeId": "4edc3c3f-6e0e-4e96-a949-63e955606aac",
    "accountingEntryLines": [
      {
        "thirdId": "901d2323-67ee-4426-aac6-e3745b8e6fd9",
        "description": "Facture FA0022 regularisation",
        "creditAmount": 0,
        "debitAmount": 158.25,
        "subAccountId": "6e3533f3-621b-4c0b-bd71-435ec982c85b"
      },
      {
        "creditAmount": 150,
        "debitAmount": 0,
        "subAccountId": "6e3533f3-6221-4c0b-bd71-435ec982c85b"
      },
      {
        "creditAmount": 8.25,
        "debitAmount": 0,
        "subAccountId": "6e3533f3-621e-4c0b-bd71-435ec982c85b"
      }
    ]
  }
}
```

**Example Response:**

```json
{
  "data": {
    "createAccountingEntryUsingIds": {
      "id": "e451aff7-004d-4e72-a6fb-4ea25e9d9c64",
      "number": 785
    }
  }
}
```

### Tax Registers (Automatic Creation)

The creation of tax registers for purchase and sales invoices is now automatic. Registers are created if the prerequisites are met; otherwise, priority is given to the creation of the accounting entry without creating the registers, rather than allowing the Sage Active business controller to reject the entry for non-compliance with the registers.

The conditions are:

- Tax codes for revenue or expense accounts must be defined.
- The VAT accounts defined in the tax codes of these accounts must be those present in the entry.
- The VAT amount and the associated taxable base for the VAT account and the expense or revenue account must be consistent with the VAT rate of the tax code.
- Other management rules not respected or not yet accounted for by Sage Active.

### Required Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | *(Deprecated)* Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### createAccountingEntryUsingIds Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| date | DateTime | Yes | Date of the accounting entry (date only, time ignored) |
| journalTypeId | UUID | Yes | Id of an existing journal |
| documentDate | DateTime | No | Date of the accounting document. If not provided, automatically set to match the entry date. (date only, time ignored) |
| documentNumber | String | No | Document number or reference of the entry. Mandatory in DE legislation. For purchases or sales, can be filled with the invoice number. |
| description | String | No (FR/ES), Yes (DE) | Description. Used as a template for lines from the interface, assigns default value to lines. |
| accountingEntryLines[] | Array | Yes | List of all entry lines. The entry must be balanced (debits = credits). |

**Info on sessionId**: The accounting session associated with the entry (DE legislation only). This value is mandatory for DE and automatically assigned: if journalId equals defaultPayrollJournalTypeId, then sessionId is set to defaultPayrollSessionId; otherwise it is set to the default value from the list of sessions AccountingEntrySession.

### createAccountingEntryUsingIds/accountingEntryLines Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| subAccountId | UUID | Yes | Id of an existing general account, required on each entry line |
| thirdId | UUID | No | Id of an existing third party (customer, supplier or employee). Necessary if the entry pertains to a third party. |
| description | String | No | Description of the line. If not specified, assigned from the header description. |
| debitAmount | Decimal | Yes | Amount debit. Entry must be balanced (debits = credits). |
| creditAmount | Decimal | Yes | Amount credit. Entry must be balanced (debits = credits). |

> **Note:** For PURCHASE_INVOICE or SALES_INVOICE journals, if several lines contain the same third party, these lines are merged into one with the sum of the debit or credit. This allows compliance with Sage Active while permitting the addition of entries from an import file containing invoices where the due dates are split over several lines.

### createAccountingEntryUsingIds Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | | Unique identifier of the entry |
| number | Int | | Entry number generated by the app |

---

## Accounting Entry Sessions

### HTTP Operations

| HTTP | Operation | Type | Object |
|------|-----------|------|--------|
| POST | Read | Query | accountingEntrySessions filtered by id |
| POST | List | Query | accountingEntrySessions |

### Description

The AccountingEntrySession represents the sessions management used to add identifiable information to your journal entries. It provides key information such as default status, name, stamp, and whether it has been deactivated. For example, you can add a session to identify that an entry is part of the year-end closing process or to identify that it is created by a specific user.

### Required Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | *(Deprecated)* Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### accountingEntrySessions Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Unique identifier for the session |
| creationDate | DateTime | System | The date and time when the session was created |
| modificationDate | DateTime | System | The date and time of the last modification |
| isDefault | Boolean | | Indicates if the session is the default session for accounting entries |
| name | String | | Name of the session (e.g., Automatisch erstellt) |
| stamp | String | | The session name and timestamp (e.g., Automatisch erstellt 12.06.2024 10:20:04) |
| deactivated | Boolean | | Indicates if the session is deactivated. When true, the session cannot be used for a new accounting entry. |
