# Purchase Invoices — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/purchaseinvoices
> - https://developer.sage.com/sageactive/resources/purchasedocuments
> - https://developer.sage.com/sageactive/resources/postpurchaseinvoice
> - https://developer.sage.com/sageactive/resources/purchaseinvoiceopenitems
> - https://developer.sage.com/sageactive/resources/purchaseopenitemsettlement

## Overview

Both known as Purchase Invoices in the API context and in the product interface.

This resource is pivotal for managing payments to suppliers after receiving goods or services and serves as a legally binding documentation of the purchase.

The Purchase Invoices entity utilizes a simplified model that primarily includes key information of the purchase invoice: invoice number, invoice date, fulfillment date, total amount, amount excluding tax, and taxes.

In addition, via AP Automation, it is possible to upload supplier invoices as PDF or image files (and XML files for DE legislation). The uploaded document is analyzed by the OCR engine, which automatically creates a corresponding purchase invoice draft linked to the file through the fileId and fileName fields. This process significantly reduces manual data entry and speeds up the recording of supplier invoices in Sage Active.

### Workflow

1. **Create the Invoice and Optionally View or Modify It:** Start by creating a purchase invoice using the PurchaseInvoice service. Once created, you can view and make any necessary modifications to the invoice before finalizing it.
2. **Post the Invoice to Generate Accounting Entries:** Proceed with posting the invoice using the PostPurchaseInvoice service. Posting the invoice will generate the necessary accounting entries, making the invoice part of the company's financial records.
3. **View the Invoice's Payment Due Dates:** Check the payment due dates and related information using the PurchaseInvoiceOpenItems service. This allows you to see when payments are expected and track outstanding amounts for the invoice.
4. **Apply Partial or Full Payments to the Invoice:** Manage the payments on the invoice by using the PurchaseOpenItemSettlement service. You can apply both partial and full payments, keeping track of amounts paid and any remaining balances.

---

## Purchase Invoices (purchaseInvoices)

### HTTP Operations

| Operation | Type | Object | DTO |
|-----------|------|--------|-----|
| POST | Create | Mutation | createPurchaseInvoice | PurchaseInvoiceCreateGLDtoInput |
| POST | Update | Mutation | updatePurchaseInvoice | PurchaseInvoiceUpdateGLDtoInput |
| POST | Delete | Mutation | deletePurchaseInvoice | PurchaseInvoiceDeleteGLDtoInput |
| POST | Read | Query | purchaseInvoices (filtered by id) | |
| POST | List | Query | purchaseInvoices | |

### Example: Create a Purchase Invoice

```graphql
mutation ($values: PurchaseCreateGLDtoInput!) {
  createPurchaseInvoice(input: $values) {
    id
  }
}
```

Variables:

```json
{
  "values": {
    "supplierId": "b647b9e4-6d2a-4c9d-96e3-54be55457096",
    "invoiceDate": "2024-08-19",
    "invoiceNumber": "AA123",
    "totalLiquid": 240,
    "hasCashVat": false,
    "operationDate": "2024-08-19T00:00:00+00:00",
    "vatLines": [
      {
        "invoiceDate": "2024-08-19T00:00:00+00:00",
        "vatPercentage": 20,
        "totalVat": 40,
        "totalVatBase": 200
      }
    ]
  }
}
```

Response:

```json
{
  "data": {
    "createPurchaseInvoice": {
      "id": "e451aff7-074f-4e72-a6fb-4ea25e9d9c64"
    }
  }
}
```

### Example: Create a Purchase Invoice with Accounting and Tax IDs

```graphql
mutation ($values: PurchaseCreateGLDtoInput!) {
  createPurchaseInvoice(input: $values) {
    id
  }
}
```

Variables:

```json
{
  "values": {
    "supplierId": "b647b9e4-6d2a-4c9d-96e3-54be55457096",
    "invoiceDate": "2024-08-19",
    "invoiceNumber": "AA123",
    "totalLiquid": 240,
    "hasCashVat": false,
    "operationDate": "2024-08-19T00:00:00+00:00",
    "vatLines": [
      {
        "invoiceDate": "2024-08-19T00:00:00+00:00",
        "totalVat": 40,
        "totalVatBase": 200,
        "purchaseAccountingAccountId": "550e8400-e29b-41d4-a716-446655440000",
        "taxId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
        "taxTreatmentId": "9a19d7f2-c524-41fc-9e7f-d59775cd8953"
      }
    ]
  }
}
```

Response:

```json
{
  "data": {
    "createPurchaseInvoice": {
      "id": "e451aff7-074f-4e72-a6fb-4ea25e9d9c64"
    }
  }
}
```

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### Fields: purchaseInvoices

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | ID |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| sourceType | Enum (null, AP_AUTOMATION, PUBLIC_API, SAGE_MOBILE_APP_AP_AUTOMATION, PUBLIC_API_AP_AUTOMATION, EMAIL_AP_AUTOMATION, E_INVOICING_AP_AUTOMATION) | System | Indicates the origin of the purchaseInvoice (null = Manual Entry) |
| invoiceDate | DateTime | | Date when the invoice was issued (only date value is considered) |
| firstDueDate | DateTime | | The first due date for payment of the invoice (only date value is considered) |
| invoiceNumber | String (50) | | Document number of the invoice |
| operationDate | DateTime | | Fulfillment date. The date cannot be later than the invoice date. (only date value is considered) |
| supplier | Supplier | | Fields of supplier (DATALOADER) |
| supplierId | UUID | | ID of the supplier entity |
| pendingAmount | Decimal | | Amount still due on the invoice |
| status | String | Read-only | Pending, Closed, Posted, PartiallyPaid, Paid, Uploading, SbdRejected |
| description | String | Read-only | Provides additional details about the current status (businessErrors code) |
| totalLiquid | Decimal | | Total amount due including taxes |
| hasCashVat | Boolean | | Indicates if the invoice is subject to Cash VAT |
| fileId | String | Read-only | ID of the file used for OCR recognition |
| fileName | String (255) | Read-only | Name of the file used for OCR recognition |
| vatLines[] | Array | Required | VAT lines of purchase Invoice |
| openItems[] | Array | | Open items of purchase Invoice |

### Status Values

**OCR Recognition:**
- **Uploading:** The invoice has been created but only contains the file name; it has not yet been processed.
- **SbdRejected:** Returned when the uploaded document was rejected by the antivirus (for example, if the file was password-protected or infected).

**Purchase Invoice:**
- **Pending:** The invoice can be modified; it has not yet been transferred to accounting.
- **Closed:** The invoice was supposed to be transferred to accounting, but the operation failed or was canceled.
- **Posted:** (Awaiting payment) The invoice can no longer be modified; it has been transferred to accounting.
- **PartiallyPaid:** The invoice is partially paid. Payments are pending.
- **Paid:** The invoice is fully paid.

> **Attention:** The Sage Active interface overrides the values Posted and PartiallyCollected of the status field with Overdue and PartiallyOverdue if the value of the firstDueDate field is earlier than the current date. However, these values Overdue and PartiallyOverdue are not returned by the API, which instead returns Posted and PartiallyCollected, respectively.

To filter invoices that are overdue:

```graphql
query {
  purchaseInvoices(
    where: {
      status: { in: ["Posted","PartiallyCollected"] }
      firstDueDate: { lt: "2024-10-09" }
    }
  )
}
```

### Description Field

Returns a message in the form of a businessErrors code. For example:
- `sales.businessErrors.simplifiedPurchaseInvoiceOcrStatusErrorMessage` - "Data extraction unsuccessful. Enter manually the required information to post the invoice."
- `sales.businessErrors.simplifiedPurchaseInvoiceManuallyAddedMessage` - "Enter the required information to post the invoice."

Although these values are returned under businessErrors, they do not always represent an error. You can use localizedErrorMessage to resolve the associated label in the desired language.

### File Information

- **fileId:** ID of the file used for OCR recognition. Example value: `4dd0a4792b404a3abd50b5991a381990.pdf`
- **fileName:** Name of the file used for OCR recognition. Example value: `invoice2025-01-1200.pdf`

File information can also be retrieved directly via files by applying a filter on id:

```graphql
query ($fileId: String!) {
  files(
    where: { id: { eq: $fileId } }
  ) {
    edges {
      node {
        fileName
        type
        size
        downloadPath
        previewPath
      }
    }
  }
}
```

### Fields: purchaseInvoices/vatLines

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | ID |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| vatPercentage | Decimal | Mutation-only | VAT percentage (only for mutations). Used to simplify specifying the tax percentage for the line. If provided, taxId should not be set as it will be automatically inferred. |
| totalVat | Decimal | Required | Total VAT amount calculated for the invoice |
| totalVatBase | Decimal | Required | Total VAT base amount (before tax) for the invoice |
| invoiceDate | DateTime | | Date when the invoice was issued (only date value is considered) |
| tax | Tax | | Fields of Tax including percentage (DATALOADER) |
| taxId | UUID | | ID for the tax applied to the invoice. Not required in mutations if vatPercentage is set. |
| taxTreatment | TaxTreatment | | Fields of TaxTreatment (DATALOADER) |
| taxTreatmentId | UUID | | ID for the tax treatment applied. Optional in mutations; system will select the first consistent with the tax rate. |
| purchaseAccountingAccount | AccountingAccount | | Fields of AccountingAccount (DATALOADER) |
| purchaseAccountingAccountId | UUID | | ID Account. Optional in mutations; system will automatically choose the first account consistent with the tax rate. |
| transactionCategory | Category | | Fields of TransactionCategory (DATALOADER) |
| transactionCategoryId | UUID | Read-only | Transaction category used to determine default accounting settings. References a transaction category of type PURCHASE. Currently available in read-only mode only. |

> **Note on tax fields:** In queries, use `taxes{percentage}` to retrieve the tax percentage, as vatPercentage is only available for mutations.

### Fields: purchaseInvoices/openItems

An Open Item is a financial transaction that allows you to know if it has been fully settled or paid. Each open item includes detailed information such as the total amount, accumulated amount paid, and the payment due date. For a single invoice, there can be as many open items as there are different payment installments.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | ID |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| amount | Decimal | | Total amount due for the open item |
| paidAmountAccumulated | Decimal | | Total amount that has been paid towards the item up to the current date |
| dueDate | DateTime | | Due date for the payment (only date value is considered) |
| status | Enum (NOT_SPECIFIED, COLLECTED, PAID, PARTIAL) | | Status of the payment |
| paymentMean | PaymentMean | | Fields of PaymentMean (DATALOADER) |
| paymentMeanId | UUID | | Identifier of the payment method used |
| purchaseInvoice | PurchaseInvoice | | Fields of PurchaseInvoice (DATALOADER) |
| purchaseInvoiceId | UUID | | Identifier of the associated purchase invoice |

**Status values:**
- **NOT_SPECIFIED:** No specific payment status has been detailed.
- **COLLECTED:** Not used for Suppliers.
- **PAID:** Payment has been fully made to suppliers.
- **PARTIAL:** Payment for the item has been made partially.

---

## Post Purchase Invoice (postPurchaseInvoice)

### HTTP Operations

| Operation | Type | Object | DTO |
|-----------|------|--------|-----|
| POST | Create | Action (Mutation) | postPurchaseInvoice | PostPurchaseInvoiceGLDtoInput |

### Description

The PostPurchaseInvoice service allows for the posting of a Purchase invoice to generate ledger entries that are associated with the Purchase invoice journal. This service also creates ledger entries based on the provided Purchase invoice details, facilitating financial tracking and management within the system.

This functionality can be related to Open Items Settlement as it is necessary for an invoice to be posted before settlements can be applied to it.

### Functionality

- **Ledger Entry Creation:** Automatically generates a ledger entry for the Purchase invoice.
- **Detailed Tracking:** Links the ledger entry directly to the Purchase invoice using the journal type specified.

### Input Parameters

- **PurchaseInvoiceId:** Mandatory, the unique identifier of the Purchase invoice to be posted.
- **accountingEntryDescription:** Description of the ledger entry being created.
- **journalTypeId:** Mandatory, the journal the ledger entry is associated with, must be PURCHASE_INVOICE type.
- **postingDate:** Accounting Date.

### Response

The service can return the following fields:
- **accountingEntryId:** Unique identifier of the created accounting entry
- **accountingEntryNumber:** Sequential number of the accounting entry

These fields confirm that the purchase invoice has been successfully posted.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### Example: Post a Purchase Invoice

```graphql
mutation($input: PostPurchaseInvoiceGLDtoInput!) {
  postPurchaseInvoice(input: $input) {
    accountingEntryId
    accountingEntryNumber
  }
}
```

Variables:

```json
{
  "input": {
    "PurchaseInvoiceId": "96771689-53b9-4f4c-add3-40eb31e38254",
    "accountingEntryDescription": "Purchase Invoice No. 0007 27/06/2024",
    "journalTypeId": "6fdf8926-cfdd-4ef8-8f89-39d6bfa622da",
    "postingDate": "2024-11-20"
  }
}
```

Response:

```json
{
  "data": {
    "postPurchaseInvoice": {
      "accountingEntryId": "0e1987c3-fc14-4906-8b26-42e4a4c36079",
      "accountingEntryNumber": 819
    }
  }
}
```

### Fields: postPurchaseInvoice Input

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| purchaseInvoiceId | UUID | Required | Unique identifier of the Purchase invoice |
| accountingEntryDescription | String (200) | Required | Description of the accounting entry being created |
| journalTypeId | UUID | Required | Identifier for the PURCHASE_INVOICE journal |
| postingDate | DateTime | | Accounting Date |

### Fields: postPurchaseInvoice Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| accountingEntryId | UUID | | Unique identifier of the ledger entry created |
| accountingEntryNumber | Int | | Ledger entry number |

---

## Purchase Invoice Open Items (purchaseInvoiceOpenItems)

### HTTP Operations

| Operation | Type | Object | DTO |
|-----------|------|--------|-----|
| POST | Read | Query | purchaseInvoiceOpenItems (filtered by id) | |
| POST | List | Query | purchaseInvoiceOpenItems | |

### Description

An Open Item is a financial transaction that allows you to know if it has been fully settled or paid. Each open item includes detailed information such as the total amount, accumulated amount paid, and the payment due date. For a single invoice, there can be as many open items as there are different payment installments, which is why openItems is structured as an array to accommodate multiple entries per invoice. The payment status provides insight into the progress of settling each item. Additionally, the identifier of the payment method used is provided to track the settlement method.

### Fields: purchaseInvoiceOpenItems

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | ID |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| amount | Decimal | | Total amount due for the open item |
| paidAmountAccumulated | Decimal | | Total amount that has been paid towards the item up to the current date |
| dueDate | DateTime | | Due date for the payment (only date value is considered) |
| status | Enum (NOT_SPECIFIED, COLLECTED, PAID, PARTIAL) | | Status of the payment |
| paymentMean | PaymentMean | | Fields of PaymentMean (DATALOADER) |
| paymentMeanId | UUID | | Identifier of the payment method used |
| purchaseInvoice | PurchaseInvoice | | Fields of PurchaseInvoice (DATALOADER) |
| purchaseInvoiceId | UUID | | Identifier of the associated purchase invoice |

**Status values:**
- **NOT_SPECIFIED:** No specific payment status has been detailed.
- **COLLECTED:** Not used for Suppliers.
- **PAID:** Payment has been fully made to suppliers.
- **PARTIAL:** Payment for the item has been made partially.

---

## Purchase Open Item Settlement (purchaseOpenItemSettlement)

### HTTP Operations

| Operation | Type | Object | DTO |
|-----------|------|--------|-----|
| POST | Create | Action (Mutation) | purchaseOpenItemSettlement | PurchaseOpenItemSettlementGLDtoInput |

### Description

The PurchaseOpenItemSettlement service is designed to manage the settlement of open items in financial accounts, allowing users to adjust payments against outstanding invoices partially or fully.

The purpose of this action is to target open items that have not yet been fully paid (status = NOT_SPECIFIED or PARTIAL) related to a purchase invoice. A total or partial amount for each due date can be specified. The action will then create a bank entry and modify the open items to align them with the elements mentioned in the action.

### How to Retrieve the IDs of Open Items

**Retrieving open items from accounting entries:**
This method is used when purchase invoices have not been recorded in Sage Active, and only the accounting entries related to these invoices have been entered or imported. Open items are accessible via `accountingEntries/accountingEntryLines/accountingEntryInvoice/openItems`.

**Retrieving open items directly from purchase invoices:**
This approach is applicable when purchase invoices are available in Sage Active. It allows for direct access to open items without going through the accounting entries. Use `purchaseOpenItemSettlement` for direct retrieval.

### Functionality

- **Settlement of Open Items:** Allows the specification and settlement of open items, adjusting the financial records accordingly.
- **Supports Partial and Full Payments:** Users can specify either partial or full payment amounts for each open item.
- **Automated Financial Entry Creation:** Automatically creates a financial entry in the accounting ledger for global settlement made.

### Input Parameters

**Settlement Details:**
- **entryDate:** Mandatory, the date of the entry for the settlement.
- **paymentMethodId:** Mandatory, the identifier for the bank account or cash where the settlement will be deposited.
- **description:** Optional, description of the settlement action.
- **documentNumber:** Optional, a reference number for the settlement document.
- **thirdPartyId:** Mandatory, the identifier of the third party involved in the transaction.

**Open Items:** Mandatory, specify the open items and the amounts for settlement.
- **purchaseOpenItemLinkagePaidAmounts:** List of open items and the amounts paid.

### Response

The service can return the following fields:
- **accountingEntryId:** Unique identifier of the created accounting entry
- **accountingEntryNumber:** Sequential number of the accounting entry

These fields confirm that the settlement has been successfully posted.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### Example: Settle Open Items

```graphql
mutation($input: PurchaseOpenItemSettlementGLDtoInput!) {
  purchaseOpenItemSettlement(input: $input) {
    accountingEntryId
    accountingEntryNumber
  }
}
```

Variables:

```json
{
  "input": {
    "entryDate": "2024-01-01",
    "paymentMethodId": "815c1835-90dd-4e62-8504-79a3f97fac67",
    "description": "Full settlement of Invoice IN0022",
    "documentNumber": "IN00022",
    "thirdPartyId": "815c1835-9cb5-4e62-8504-79a3f97fac67",
    "purchaseOpenItemLinkagePaidAmounts": [
      {
        "openItemId": "815c1835-a4e7-4e62-8504-79a3f97fac67",
        "paidAmount": 500
      },
      {
        "openItemId": "815c1835-a647-4e62-8504-79a3f97fac67",
        "paidAmount": 792
      }
    ]
  }
}
```

Response:

```json
{
  "data": {
    "purchaseOpenItemSettlement": {
      "accountingEntryId": "0e1987c3-ed48-4906-8b26-42e4a4c36079",
      "accountingEntryNumber": 818
    }
  }
}
```

### Fields: purchaseOpenItemSettlement Input

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| entryDate | DateTime | Required | Date of the entry |
| paymentMethodId | UUID | Required | Bank Account and cash Id |
| description | String | | Description of the settlement action |
| documentNumber | String | | Reference number for the document |
| thirdPartyId | UUID | Required | Third party involved |
| purchaseOpenItemLinkagePaidAmounts[] | Array | | List of open items and the paid amounts |

### Fields: purchaseOpenItemLinkagePaidAmounts

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| openItemId | UUID | | Unique identifier of the open item to settle |
| paidAmount | Decimal | | Amount paid for the specified open item |

### Fields: purchaseOpenItemSettlement Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| accountingEntryId | UUID | | Unique identifier of the accounting entry created |
| accountingEntryNumber | Int | | Number assigned to the accounting entry |
