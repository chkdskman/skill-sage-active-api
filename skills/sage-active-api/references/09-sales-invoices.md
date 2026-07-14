# Sales Invoices — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/salesinvoices
> - https://developer.sage.com/sageactive/resources/closesalesinvoice
> - https://developer.sage.com/sageactive/resources/postsalesinvoice
> - https://developer.sage.com/sageactive/resources/advanceinvoice
> - https://developer.sage.com/sageactive/resources/invoicewithonlinepayment

## HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | `createSalesInvoice` | `SalesInvoiceCreateGLDtoInput` |
| POST | Update | Mutation | `updateSalesInvoice` | `SalesInvoiceUpdateGLDtoInput` |
| POST | Delete | Mutation | `deleteSalesInvoice` | `SalesInvoiceDeleteGLDtoInput` |
| POST | Read | Query | `salesInvoices` filtered by id | |
| POST | List | Query | `salesInvoices` | |
| POST | List | Query | `salesInvoiceLines` | |

## Quick Tips

- It is possible to set the invoice number using the `operationalNumber` field during invoice creation. This enhancement allows users to import an invoice already issued by another software while keeping its original number.
- If `operationalNumber` is provided with a non-empty value, the invoice is automatically set to Closed status.
- If `operationalNumber` is not specified or left empty, the invoice is created without a number and remains in Draft status.
- It is possible to query either `salesInvoices` or directly from `salesInvoiceLines`.
- Querying `salesInvoices` allows you to retrieve entire documents with all their associated line items. This is useful when you need the complete context of a document, including all details of the related lines.
- Querying `salesInvoiceLines` directly is beneficial when you want to filter or aggregate specific line item data across multiple documents, such as analyzing product sales, quantities, or prices.

## Description

Both known as Sales Invoices in the API context and in the product interface. This resource is pivotal for invoicing customers after order fulfillment and serves as a legally binding request for payment.

The Sales Invoices entity leverages both standard and custom fields, allowing businesses to create detailed invoices that include necessary information like customer codes, discounts, product lines, and tax details. This ensures compliance and contributes to a smoother accounts receivable process.

## Workflow

1. **Create the Invoice and Optionally View or Modify It:** Start by creating a sales invoice using the SalesInvoice service. Once created, you can view and make any necessary modifications to the invoice before finalizing it.
2. **Close the Invoice to Prevent Further Modifications:** Use the CloseSalesInvoice service to confirm the invoice. This action locks the invoice, ensuring that its content cannot be changed. This step is essential for securing the invoice details before posting.
3. **Post the Invoice to Generate Accounting Entries:** After closing, proceed with posting the invoice using the PostSalesInvoice service. Posting the invoice will generate the necessary accounting entries, making the invoice part of the company's financial records.
4. **View the Invoice's Payment Due Dates:** Check the payment due dates and related information using the SalesInvoiceOpenItems service. This allows you to see when payments are expected and track outstanding amounts for the invoice.
5. **Apply Partial or Full Payments to the Invoice:** Manage the payments on the invoice by using the SalesOpenItemSettlement service. You can apply both partial and full payments, keeping track of amounts paid and any remaining balances.
6. **Generate a Credit Note if Necessary:** In case of returns, refunds, or corrections, you can issue a credit note related to the invoice using the `generateCreditNote` service.

To see examples of mutations that include the adding of Invoice, download the Postman collection: Quick start / 5. Test your first query in Postman.

To change the Status of a salesInvoice, refer to the global sales documents page, which details the available options depending on the document type.

### Example: Create a Sales Invoice

**GraphQL Mutation:**

```graphql
mutation ($values: SalesInvoiceCreateGLDtoInput!) { 
  createSalesInvoice(input: $values) {
    id
  }
}
```

**GraphQL Variables:**

```json
{
  "values": {
    "customerId": "{customerId}",
    "documentDate": "2023-12-05",
    "lines": [
      {
        "productId": "{productId1}",
        "totalQuantity": 50,
        "unitPrice": 11,
        "firstDiscount": 5
      },
      {
        "productId": "{productId2}",
        "totalQuantity": 1,
        "unitPrice": 140
      }
    ]
  }
}
```

**Example Response:**

```json
{
  "data": {
    "createSalesInvoice": {
      "id": "e451aff7-074f-4e72-a6fb-4ea25e9d9c64"
    }
  }
}
```

> **Note:** For salesInvoices, the `operationalNumber` is not assigned at the time of creation, unlike other document types. It is only assigned after the `closeSalesInvoice` operation.

```json
{
  "data": {
    "closeSalesInvoice": {
      "id": "e451aff7-07f0-4e72-a6fb-4ea25e9d9c64",
      "operationalNumber": "0037"
    }
  }
}
```

## Request Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

## salesInvoices Fields

### Common to All Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| sourceType | SALES, PUBLIC_API | System | Indicates the origin of the salesInvoice (SALES=Manual entry) |

### General Info

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| customer | Customer | | DATALOADER - Fields of Customer |
| customerId | UUID | Required, Non-modifiable | ID of the Customer entity. Not mandatory for Quote |
| documentDate | DateTime | | Document Date (date only, time ignored) |
| operationalNumber | String | | Document Number (max 50) |
| operationalNumberPresetText | OperationalNumberPresetText | | DATALOADER — Fields of OperationalNumberPresetText (live since 2026-06) |
| operationalNumberPresetTextId | UUID | | ID of the operational number preset text (live since 2026-06) |
| status | String | | Pending, Closed, Posted, PartiallyCollected, Collected (max 15) |
| socialName | String | Non-modifiable | Social Name (max 50) |
| contactName | String | | Contact Name (max 50) |
| contactPhone | String | | Contact Phone (max 15) |
| contactEmail | String | | Contact Email (max 50) |
| contactJobAreaId | UUID | | Id of the Contact Job Area |
| countryAcronym | String | | Country Acronym (max 2) |

### Invoice Lines

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| lines[] | Array | Required | Document Lines |

### Totals and Discount

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalNet | Decimal | | Amount after discounts, before taxes |
| totalGross | Decimal | | Total amount before discounts and taxes |
| totalDiscount | Decimal | | Sum of all discounts applied |
| totalCustomerDiscount | Decimal | | Discount specific to the customer |
| totalLiquid | Decimal | | Final amount due, including taxes |
| totalVat | Decimal | | Total Value Added Tax (VAT) applied |
| totalVatBase | Decimal | | Total taxable base before VAT |
| totalFeeSurcharge | Decimal | | Total equivalence surcharge applied, based on VAT |
| totalVatFee | Decimal | | VAT amount used as the base for the surcharge |
| discount | Decimal | | Specific discount applied |
| totalLiquidNoWithholding | Decimal | | Total amount excluding withholding |
| totalWithholding | Decimal | | Total withholding amount |

### Notes

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| comments | String | | Notes for the customer/recipient of the document (max 1000) |
| remarks | String | | Internal notes for the document (max 1000) |
| externalReference | String | | External customer reference provided to facilitate communication and tracking (max 20) |

### Customer Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| documentId | String | | Identification Number (max 20) |
| documentLanguage | ENGLISH, FRENCH, GERMAN, SPANISH | | Language used in sales Document Pdf (max 20) |
| documentType | DocumentType | | DATALOADER - Fields of DocumentType used for identification purposes |
| documentTypeId | UUID | | Identifier of the document type |
| vatNumber | String | | VAT Number (max 20) |
| hasEquivalenceSurcharge | Boolean | | Subject to the equivalence surcharge regime |
| mainAddress | ObjectType | | Main Address |
| deliveryAddress | ObjectType | | Delivery Address |
| salesDiscountGroup | SalesDiscountGroup | | DATALOADER - Fields of SalesDiscount |
| salesDiscountGroupId | UUID | | Sales Discount ID |
| salesTariff | SalesTariff | | DATALOADER - Fields of SalesTariff |
| salesTariffId | UUID | | Sales tariff ID |

### Specific to Sales Invoice Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| operationalNumber | String | | Invoice Number (max 50) |
| operationDate | DateTime | | Date of operation / fulfillment date (date only, time ignored) |
| salesOrderNumber | String | | External Order Number (max 250) |
| type | NONE, CREDIT_NOTE, NORMAL, PAYMENT_IN_ADVANCE | | Document type |
| salesInvoiceTemplateId | UUID | Read-only | Sales invoice template ID — when a value is returned, the invoice corresponds to a Recurring invoice; when null, it is a classic invoice (added 2026-06) |
| hasCreditNote | Boolean | | Indicates whether a credit note exists: true/false |
| firstDueDate | DateTime | | The first due date for payment of the invoice (date only, time ignored) |
| createdInSoftwareVersion | String | | Links an invoice to the software release version (max 10) |
| openItems[] | Array | | Open Items |
| printNameOnPdf | Boolean | | Print the customer name on the simplified-invoice PDF for this document. ES only. Pre-fillable from `Customer.printNameOnPdf` (live since 2026-05) |
| specialMention | String | | Free-text mention displayed on the final invoice (must be explicitly set via API) (max 1000) |
| canGeneratePaymentLink | Boolean | | (NEW) Indicates whether the invoice should generate an online payment link |
| invoiceEmail | String | | (NEW) Email address used when sending the invoice with the online payment link |
| paymentTermLines[] | Array | | Payment Term Lines |

### Specific to Sales Invoice Documents — Type CREDIT_NOTE

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| sourceSalesInvoice | SalesInvoice | | DATALOADER - Fields of salesInvoice if the type is CREDIT_NOTE |
| sourceSalesInvoiceId | UUID | | ID of the invoice if the type is CREDIT_NOTE |

### Field Info

- **customerId:** A unique identifier (UUID) for the Customer entity.
- **totalGross:** Represents the total amount before any discounts or taxes are applied. It includes the sum of all item prices at their full value.
- **totalDiscount:** The aggregated discount amount applied to the document, including all types of discounts that reduce the gross total.
- **totalNet:** The total amount after applying discounts but before adding taxes. It represents the taxable base amount.
- **discount:** Represents a specific discount amount applied within the document. This can be a percentage or a fixed amount reduction.
- **totalCustomerDiscount:** A discount specifically applied based on customer-specific conditions, such as loyalty discounts or promotional offers tailored to the customer.
- **totalLiquid:** The total payable amount, which includes taxes and any additional fees. This represents the final amount due.
- **totalVat:** The total Value Added Tax (VAT) calculated on the taxable base. This amount represents the sum of all VAT applied across items in the document.
- **totalVatBase:** The total taxable base amount before VAT is applied. This is the sum of all net amounts subject to VAT.
- **totalLiquidNoWithholding / totalWithholding:** Unused (Spain only). The following fields are specific to the Spanish legislation (IRPF): `totalLiquidNoWithholding` is the total amount excluding withholding tax (taxable base before applying IRPF withholding). `totalWithholding` is the total withholding amount (IRPF) calculated at document level.
- **hasEquivalenceSurcharge / totalFeeSurcharge / totalVatFee:** Unused for the French market. Indicates whether a customer is subject to the equivalence surcharge regime. The equivalence surcharge (`totalFeeSurcharge`), as applied in Spain, is a form of simplified VAT for retail traders who cannot recover VAT. The rates vary according to the VAT rate applicable to the products (5.2% for 21% VAT, 1.4% for 10% VAT, etc.). The equivalence surcharge is calculated on the amount of general VAT (`totalVatFee`). Unused for the German market.
- **socialName:** Mandatory for the French market. Not mandatory for the Spanish market. Not mandatory for the German market.
- **externalReference:** Allows entering an external reference provided by the customer to facilitate communication and tracking.
- **vatNumber:** An EU VAT Number is a unique identifier for tax purposes within the EU, used primarily for cross-border transactions. It starts with a two-letter country code followed by a series of numbers and sometimes letters, varying by country. Not applicable for customers not registered in the VIES system. For VIES customers, the format is described at https://ec.europa.eu/taxation_customs/vies/#/faq (FAQ 11). Mandatory for VIES customers. Not mandatory for Spanish market. Not mandatory for German market.
- **documentId:** Identification number, value formats may differ depending on legislationCode. For French: any SIREN (9 chars) or SIRET (14 chars), not mandatory. For German: Steuer-IdNr (11 chars) or another value (11-14 chars), not mandatory. For Spanish: valid NIF/DNI/NIE number, mandatory.
- **documentLanguage:** Applies to section titles and column headers in sales documents PDFs.
- **documentTypeId:** Determines the corresponding document type based on the document number provided. Vital for verifying the real existence of a customer.
- **operationalNumber:** It is possible to set the invoice number using this field during invoice creation. If provided with a non-empty value, the invoice will automatically be set to Closed status. If not specified or left empty, the invoice will be created without a number and remain in Draft status.
- **operationDate:** The date on which the delivery of goods or the provision of services is carried out or completed. Must be on or before the invoice date (Fulfillment date). It is possible to specify an operationDate when it differs from the invoice date.
- **salesOrderNumber:** Allows the entry of an order number that has not been entered in Sage Active.
- **specialMention:** Free-text field displayed on the final invoice sent to the customer. In the application UI, this field is pre-filled with the default mention defined at customer level. Via the API, this value is not automatically inherited: you must explicitly provide it when creating the sales invoice.
- **createdInSoftwareVersion:** Links an invoice to the software release version that generated it. Introduced to comply with anti-fraud regulations by ensuring traceability.

### Status Values

| Status | Description |
|--------|-------------|
| Pending | (Draft) The invoice can be modified; it has not yet been transferred to accounting. |
| Closed | (Unposted) The invoice can no longer be modified; it was supposed to be transferred to accounting, but the operation failed or was canceled. |
| Posted | (Awaiting collection) The invoice can no longer be modified; it has been transferred to accounting. |
| PartiallyCollected | (Partially collected) The invoice is partially settled. Payments are pending. |
| Collected | (Collected) The invoice is fully settled. |

> **Attention:** The Sage Active interface overrides the values `Posted` and `PartiallyCollected` of the status field with `Overdue` and `PartiallyOverdue` if the value of the `firstDueDate` field is earlier than the current date. However, these values `Overdue` and `PartiallyOverdue` are not returned by the API, which instead returns `Posted` and `PartiallyCollected`, respectively.

To filter invoices that are overdue, use the following filter:

```graphql
query {
  salesInvoices(
    where: {
      status: { in: ["Posted","PartiallyCollected"] }
      firstDueDate: { lt: "2024-10-09" }
    }
  )
}
```

### Payment Link Fields Info

- **canGeneratePaymentLink:** If set to `true`, the system will generate an online payment link in the Invoice PDF. The link will only be generated if the Invoice contains a single payment condition using a Payment Mean with `type = PAYMENT_IN` and if the Invoice is posted. If set to `false`, no online payment link will be generated.
- **invoiceEmail:** Email address used when sending the Invoice PDF through DocumentPdfEmail. This email is required when `canGeneratePaymentLink = true`, as it is used for online payment communication. If not provided, the Invoice can still be created, but the online payment email workflow will not be available.

## salesInvoiceLines Fields

If the query targets `salesInvoices`, then use `lines` to get the details of the lines. If the query targets `salesInvoiceLines`, access the following fields directly, including the option to retrieve information on the parent `salesInvoice`.

### Common to All Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Date of creation (system-assigned) |
| modificationDate | DateTime | System | Last modification date (system-assigned) |

### Parent Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| salesInvoice | SalesInvoice | | DATALOADER - Fields of the parent salesInvoices |
| salesInvoiceId | UUID | | salesInvoice ID |

### Product Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| product | Product | | DATALOADER - Fields of Product |
| productId | UUID | | Product ID |
| productCode | String | | Product code (max 15) |
| productName | String | | Product name (max 2500) |
| order | Int | Read-only | Classification of lines from 1 to n |
| unitOfMeasurement | UnitOfMeasurement | | DATALOADER - Fields of Unit of Measurement |
| unitOfMeasurementId | UUID | Default-calculated | Unit of Measurement ID |

### Tax and Price Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalQuantity | Decimal | | Quantity product |
| unitPrice | Decimal | | Unit price |
| vatPercentage | Decimal | Read-only | VAT percentage |
| equivalenceSurchargePercentage | Decimal | Read-only | Surcharge percentage |
| tax | Tax | Read-only | Fields of Tax (DATALOADER) — added 2026-06 |
| taxId | UUID | Read-only | Tax ID — added 2026-06 |
| taxTreatmentId | UUID | Read-only | Tax treatment ID — added 2026-06 |

### Totals

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalNet | Decimal | | Amount after discounts, before taxes |
| totalGross | Decimal | | Total amount before discounts and taxes |
| totalDiscount | Decimal | | Sum of all discounts applied |
| totalCustomerDiscount | Decimal | | Discount specific to the customer |
| totalLiquid | Decimal | | Final amount due, including taxes |
| totalVat | Decimal | | Total Value Added Tax (VAT) applied |
| totalVatBase | Decimal | | Total taxable base before VAT |
| totalFeeSurcharge | Decimal | | Total equivalence surcharge applied, based on VAT |
| totalVatFee | Decimal | | VAT amount used as the base for the surcharge |
| totalLiquidNoWithholding | Decimal | | Line total excluding withholding |
| totalWithholding | Decimal | | Line withholding amount |
| withholdingPercentage | Decimal | | Withholding percentage applied to the line |

### Discounts

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| firstDiscount | Decimal | | First discount percentage |

### Specific to Sales Invoice Document Lines

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| availableForCreditNoteQuantity | Decimal | | Quantity available for credit note |
| originId | UUID | Mutation-only (NEW) | ID of the advance invoice linked to this deduction line |

### Line Fields Info

- **unitOfMeasurementId:** UUID specifying the ID of the unit of measurement associated with the product.
- **firstDiscount:** Various levels of discounts applicable to the document line. These are optional fields and are usually filled in based on promotional schemes or contractual obligations.
- **unitPrice:** Required to set the line amount. The API does not auto-populate this from the product's `salesUnitPrice` — if omitted, the line is created with price 0. Query `products.salesUnitPrice` first if you don't know it.
- **productId:** The unique identifier for the product. Automatically filled when selecting a product.
- **vatPercentage:** The percentage of Value Added Tax (VAT) applicable to this line. Important for accounting and for generating accurate invoices.
- **equivalenceSurchargePercentage:** The percentage of equivalence surcharge applied in Spain.
- **totalLiquidNoWithholding / totalWithholding / withholdingPercentage:** Unused (Spain only). The following fields are specific to the Spanish legislation (IRPF): `totalLiquidNoWithholding` is the line total amount excluding withholding tax. `totalWithholding` is the withholding amount (IRPF) calculated for the document line. `withholdingPercentage` is the withholding percentage (IRPF) applied to the line and used to calculate the withholding amount.
- **order:** This field represents the position of a line within a document. It is read-only and is automatically calculated during creation or update. Its purpose is to reflect the order in which the user entered the lines, as shown in the Sage Active interface.

#### Preserving Line Order During Creation or Update

Although the `order` field cannot be set manually, its value is inferred from the order of lines in the payload.

- **On creation:** The position of lines in the payload determines their order. The first line gets `order = 1`, the second `order = 2`, etc. Make sure to sort the lines in the desired order before sending the payload.
- **On update:** Lines are re-ordered from 1 to n based on the payload. Since the update follows a PATCH logic, you may choose between sending only the changes (modified lines, new lines, lines marked for deletion) or sending all the lines (including unchanged ones) in the desired order. In both cases: lines with `requestedAction = DELETED` are excluded from the new numbering since they will be removed. New lines are appended after the last remaining line and automatically numbered accordingly.

#### Specific to Sales Invoice Document Lines Info

- **availableForCreditNoteQuantity:** The quantity from this sales invoice line that is still available to be credited via a credit note.
- **originId:** Used when deducting an advance payment. It must contain the ID of the advance invoice that was previously created and stored. This field is only used during creation and is not available in read operations. This ensures proper linkage between the advance and the final invoice and guarantees accounting consistency.

## salesInvoices/openItems

An Open Item is a financial transaction that allows you to know if it has been fully settled or paid. Each open item includes detailed information such as the total amount, accumulated amount paid, and the payment due date. For a single invoice, there can be as many open items as there are different payment installments, which is why `openItems` is structured as an array to accommodate multiple entries per invoice. The payment status provides insight into the progress of settling each item. Additionally, the identifier of the payment method used is provided to track the settlement method.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| amount | Decimal | | Total amount of the item |
| paidAmountAccumulated | Decimal | | Accumulated amount that has been paid |
| dueDate | DateTime | | Due date for the payment (date only, time ignored) |
| status | NOT_SPECIFIED, COLLECTED, PAID, PARTIAL | | Status of the payment |
| isAdvanceInvoicePayment | Boolean | | If the invoice is an Advance Invoice Payment |
| paymentMean | PaymentMean | | DATALOADER - Fields of PaymentMean |
| paymentMeanId | UUID | | Identifier of the payment method used |
| salesInvoice | SalesInvoice | | DATALOADER - Fields of SalesInvoice |
| salesInvoiceId | UUID | | Identifier of the associated sales invoice |

### Open Items Info

- **amount:** Total amount due for the open item.
- **creationDate:** Indicates when the item was initially created.
- **modificationDate:** Timestamp of the last update to the item, null if no updates have been made.
- **paidAmountAccumulated:** Total amount that has been paid towards the item up to the current date.
- **dueDate:** Date by which the payment for the item is due.
- **status:** Represents the current status of the payment:
  - `NOT_SPECIFIED`: No specific payment status has been detailed.
  - `COLLECTED`: Payment has been fully collected from customers.
  - `PAID`: Not used for Customers.
  - `PARTIAL`: Payment for the item has been made partially.

## salesInvoices/addresses

### Common to All Documents

> **Important:** Do not use the `addresses[]` array. Instead, use `mainAddress` for the primary address and `deliveryAddress` for the delivery address. Each of these objects contains only one address, representing a singular primary or delivery address, respectively.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| countryId | UUID | | Country Id |
| country | Country | | DATALOADER - Fields of Country |
| countryIsoCodeAlpha2 | String | | Country Code (max 2) |
| countryName | String | Read-only | Country Name |
| name | String | | Code (max 50) |
| firstLine | String | | First Line (max 35) |
| secondLine | String | | Second Line (max 35) |
| city | String | | Town (max 35) |
| zipCode | String | | Postal Code (max 9) |
| province | String | | Province / Region / Federal State (max 25) |

### Address Info

- **countryIsoCodeAlpha2:** ISO2 country code. This field can be used for creation and serves as a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.

## salesInvoices/paymentTermLines

(NEW) Only for Sales Invoice. Payment terms, used to define the list of payment terms for SalesInvoice.

Example:
- Advance payment by check 30%
- 30% by transfer at 30 days net
- The balance of 40% by bank transfer at 60 days end of month

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| order | Int | Required | Classification of lines from 0 to n |
| paymentMean | PaymentMean | | DATALOADER - Fields of PaymentMean |
| paymentMeanId | UUID | | Id of the payment mean |
| type | NONE, FIXED_AMOUNT, PERCENTAGE, REMAINING_AMOUNT | | Type of calculation for the payment term line |
| value | Decimal | Required | Numerical value used based on the type of calculation |
| condition | NONE, DAY_OF_PAYMENT, END_OF_MONTH | | To calculate due date |
| day | Int | | Number of days |
| payDays[] | Int | | Array of optional due days |

### Payment Term Lines Info

- **order:** If `REMAINING_AMOUNT`, must always be last with the value 9999.
- **payDays:** Array of optional due days. The day contained in the array closest to the number of days defined in `day` will be used to calculate the Due Date.
- **type:** Defines the method used for payment term calculations. The interpretation of `value` depends on the selected type:
  - `NONE`: No specific calculation is applied.
  - `PERCENTAGE`: `value` represents a percentage of the total amount.
  - `FIXED_AMOUNT`: `value` is a fixed monetary amount.
  - `REMAINING_AMOUNT`: The remaining balance after all prior calculations. Must always be last in the order.
- **value:** Represents the numerical input for the calculation based on type:
  - If `type = PERCENTAGE`, value should be between 0 and 100, indicating the percentage of the total amount.
  - If `type = FIXED_AMOUNT`, value is a fixed currency amount (e.g., 500 means a fixed payment of 500).
  - If `type = REMAINING_AMOUNT`, value is ignored, as the system automatically calculates the remaining balance.
- **Examples:**
  - If `type = PERCENTAGE` and `value = 30`, the payment term will cover 30% of the total amount.
  - If `type = FIXED_AMOUNT` and `value = 500`, the payment term will apply a fixed amount of 500.
  - If `type = REMAINING_AMOUNT`, the system will allocate the remaining balance after all prior lines are processed. In this case, `order` must always be set to 9999.

---

## CloseSalesInvoice

### HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Action (Mutation) | `closeSalesInvoice` | `CloseSalesInvoiceGLDtoInput` |

### Description

The CloseSalesInvoice service allows for confirming a sales invoice, meaning its content can no longer be modified. This step is essential to proceed with posting the invoice, which generates accounting entries and enables tracking payments associated with the invoice.

After closing, the invoice status changes from Pending (Draft) to Closed (Unposted), making it ready for posting and further financial processing.

### Functionality

- **Invoice Confirmation:** Locks the invoice content, preventing further modifications.
- **Status Update:** Changes the invoice status from Pending (Draft) to Closed (Unposted).
- **Prepares for Posting:** Makes the invoice eligible for posting, which will generate accounting entries and allow payment tracking.

### Input Parameters

Invoice Details: Specify the details of the sales invoice to be closed.
- **salesInvoiceId:** Mandatory, the unique identifier of the sales invoice to be closed.

### Response

The service can return the following fields:
- **id:** Unique identifier of the closed sales invoice
- **operationalNumber:** Sequential number of the invoice in the operational process

These fields confirm that the invoice has been successfully closed.

### Request Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### Example: Close a Sales Invoice

**GraphQL Mutation:**

```graphql
mutation($input: CloseSalesInvoiceGLDtoInput!) {
  closeSalesInvoice(input: $input) {
    id
    operationalNumber
  }
}
```

**GraphQL Variables:**

```json
{
  "input": {
    "salesInvoiceId": "96771689-53b9-4f4c-add3-40eb31e38254"
  }
}
```

**Example Response:**

```json
{
  "data": {
    "closeSalesInvoice": {
      "id": "96771689-53b9-4f4c-add3-40eb31e38254",
      "operationalNumber": "0038"
    }
  }
}
```

### closeSalesInvoice Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| salesInvoiceId | UUID | Required | Unique identifier of the sales invoice |

### closeSalesInvoice Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | | Unique identifier of the closed sales invoice |
| operationalNumber | String | | Sequential number of the invoice in the operational process |

### Info

- **operationalNumber:** This field provides the sequential number used to reference the invoice in operational flows.

---

## PostSalesInvoice

### HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Action (Mutation) | `postSalesInvoice` | `PostSalesInvoiceGLDtoInput` |

### Description

The PostSalesInvoice service allows for the posting of a sales invoice to generate ledger entries that are associated with the sales invoice journal. This service also creates ledger entries based on the provided sales invoice details, facilitating financial tracking and management within the system.

This functionality can be related to Sales Open Items Settlement as it is necessary for an invoice to be posted before settlements can be applied to it.

### Functionality

- **Ledger Entry Creation:** Automatically generates a ledger entry for the sales invoice.
- **Detailed Tracking:** Links the ledger entry directly to the sales invoice using the journal type specified.

### Input Parameters

Invoice Details: Specify the details of the sales invoice that is being posted.
- **salesInvoiceId:** Mandatory, the unique identifier of the sales invoice to be posted.
- **accountingEntryDescription:** Description of the ledger entry being created.
- **journalTypeId:** Mandatory, the journal the ledger entry is associated with, must be SALES_INVOICE type.

### Response

The service can return the following fields:
- **accountingEntryId:** Unique identifier of the created accounting entry
- **accountingEntryNumber:** Sequential number of the accounting entry

These fields confirm that the sales invoice has been successfully posted.

### Request Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### Example: Post a Sales Invoice

**GraphQL Mutation:**

```graphql
mutation($input: PostSalesInvoiceGLDtoInput!) {
  postSalesInvoice(input: $input) {
    accountingEntryId
    accountingEntryNumber
    accountingEntries {
      accountingEntryId
      accountingEntryNumber
    }
  }
}
```

**GraphQL Variables:**

```json
{
  "input": {
    "salesInvoiceId": "96771689-53b9-4f4c-add3-40eb31e38254",
    "accountingEntryDescription": "Sales Invoice No. 0007 27/06/2024",
    "journalTypeId": "6fdf8926-cfdd-4ef8-8f89-39d6bfa622da"
  }
}
```

**Example Response:**

```json
{
  "data": {
    "postSalesInvoice": {
      "accountingEntryId": "4b4a0742-392b-4170-bfd4-969a2c882f44",
      "accountingEntryNumber": 1516,
      "accountingEntries": [
        {
          "accountingEntryId": "4b4a0742-392b-4170-bfd4-969a2c882f44",
          "accountingEntryNumber": 1516
        }
      ]
    }
  }
}
```

### postSalesInvoice Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| salesInvoiceId | UUID | Required | Unique identifier of the sales invoice |
| accountingEntryDescription | String | Required | Description of the accounting entry being created (max 200) |
| journalTypeId | UUID | Required | Identifier for the SALES_INVOICE journal |

### postSalesInvoice Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| accountingEntryId | UUID | | Unique identifier of the primary ledger entry created |
| accountingEntryNumber | Int | | Ledger entry number of the primary ledger entry |
| accountingEntries[] | Array | | List of all related ledger entries |

### postSalesInvoice/accountingEntries Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| accountingEntryId | UUID | | Unique identifier of a related ledger entry |
| accountingEntryNumber | Int | | Ledger entry number of a related ledger entry |

### Info

In cases where sales invoices include deductions from previously invoiced deposits, two ledger entries may be generated for a single invoice:
- One for the invoice amount excluding the deposit deduction.
- One for the deposit deduction itself.

To maintain response compatibility:
- `accountingEntryId` and `accountingEntryNumber` continue to return information for the first ledger entry.
- The `accountingEntries` array includes all generated ledger entries, including the first one.

### New Logic for Sales Ledger Account Selection

The determination of the sales ledger account during invoice posting has been updated. The selection is now based on:

| Scenario | Sales Account Used |
|----------|-------------------|
| National Customer (e.g., FR for Sage Active FR) | `salesAccountingAccountId` from the product, or if not set, `defaultSaleProductsAccountId` from `organizationAccountingSetupByOrgId` |
| Customer from a VIES country (e.g., BE, IT, PT, etc.) | `intracommunityAccountId` from `organizationAccountingSetupByOrgId` |
| Customer from a non-VIES country (e.g., US, JP, etc.) | `foreignAccountId` from `organizationAccountingSetupByOrgId` |

This logic ensures a more accurate and automated assignment of the appropriate sales ledger account based on the customer's country and VIES membership.

---

## Advance Invoice

### Description

This section explains how to use the public API to create advance invoices and invoices with advance payment deductions.

In the Sage Active interface, from a quote or an order, it is possible to request the creation of an advance invoice. A form allows defining the advance amount to be invoiced. Then, when generating an invoice for the same customer, it is possible to deduct the amount of one or more advance invoices already issued to the customer.

Technically, Sage Active uses a product `ADVANCE_PAYMENT` which is not visible in the product list. Note that this value depends on the legislation, e.g. `ACOMPTE` for the French Legislation.

An advance invoice will always include a line with this `ADVANCE_PAYMENT` product and the corresponding advance amount. To deduct an advance payment from an invoice, Sage Active adds a line with a negative quantity associated with the `ADVANCE_PAYMENT` product, reflecting the amount to be deducted.

In the API context, the same mutation operation `createSalesInvoice` is used, just like for standard invoice creation. The only distinction with a classical invoice is:

- **Creating an advance invoice** requires using the `ADVANCE_PAYMENT` product, with a quantity of 1 and the amount set to the advance payment value. Once the advance invoice is created, store its returned ID, as it will be needed later when creating the final invoice.
- **Deducting an advance payment** from an invoice involves adding one or multiple lines to the invoice with the `ADVANCE_PAYMENT` product, a quantity of -1, and the positive amount to be deducted. For each deduction line (negative quantity), the `originId` field must be filled with the ID of the corresponding advance invoice that was previously stored.

### Key Points

- The field `defaultPrepaymentAccountingAccount` has been added to Customers, and the field `defaultPrepaymentCustomerAccountId` has been added to Organization Accounting Setup. They are used to post the accounting entry to an advance payment and deposit received account for advance invoices.
- The type of an advance invoice will automatically be set to `PAYMENT_IN_ADVANCE`.

| Field | Type | Notes |
|-------|------|-------|
| type | NONE, CREDIT_NOTE, NORMAL, PAYMENT_IN_ADVANCE | Document type |

### Finding the Advance Payment Product

The advance payment product can be found via a query on the Product entity by checking for the product that belongs to the `CONCEPT` category.

| Field | Type | Notes |
|-------|------|-------|
| category | NOT_SPECIFIED, CONCEPT, FEE, PRODUCT, PRODUCT_AND_SERVICE, SERVICE | Product Category |

```graphql
query {
  products(
    where: { category: { eq: CONCEPT } }
  ) {
    edges {
      node {
        id
        code
        name
      }
    } 
  } 
}
```

**Example response for the French Legislation:**

```json
{
  "data": {
    "products": {
      "edges": [
        {
          "node": {
            "id": "f50af716-34ff-4c1b-8e65-dd08220a056b",
            "code": "ACOMPTE",
            "name": "Advance Payment"
          }
        }
      ]
    }
  }
}
```

The `ADVANCE_PAYMENT` product serves two purposes:
- Creating an advance invoice, where it is used with a quantity of 1 and the amount set to the advance payment value.
- Deducting an advance payment from an invoice, where one or multiple lines are added using the `ADVANCE_PAYMENT` product with a quantity of -1, the positive amount to be deducted, and the `originId` field referencing the advance invoice ID.

### Accounting Entries

- When posting an advance invoice using `postSalesInvoice`, an accounting entry is generated, assigning the amount to the `defaultPrepaymentAccountingAccount` to record the advance payment.
- When posting an invoice that includes one or multiple advance payment deductions using `postSalesInvoice`, two accounting entries are generated:
  - A standard sales entry corresponding to the product lines of the invoice and their associated sales accounts.
  - A counterpart entry offsetting the `defaultPrepaymentAccountingAccount`, which was initially assigned when the advance invoices were created.
- To accommodate these two entries, the response of `postSalesInvoice` has been updated. In addition to returning the `accountingEntryId` and `accountingEntryNumber` for the first entry, it now includes an array of accounting entries containing the IDs and numbers of both generated entries.

### Example: Creating an Advance Invoice

Here, a single line with the advance payment product ID, a quantity of 1, and the advance payment amount.

> **Important:** The ID `f50af716-34ff-4c1b-8e65-dd08220a056b` for the advance payment line must strictly be the ID of the advance payment product retrieved by filtering on the product with the `CONCEPT` category. Once the advance invoice is created, store the returned invoice ID because it will be required later in the `originId` field when creating the final invoice that deducts this advance payment.

**GraphQL Mutation:**

```graphql
mutation ($values: SalesInvoiceCreateGLDtoInput!) { 
  createSalesInvoice(input: $values) {
    id
  }
}
```

**GraphQL Variables:**

```json
{
  "values": {
    "customerId": "3ec1023d-96b4-4a9e-b536-2f8951d63ba0",
    "documentDate": "2025-02-10",
    "lines": [
      {
        "productId": "f50af716-34ff-4c1b-8e65-dd08220a056b",
        "productName": "Advance payment for Quote 0078", 
        "totalQuantity": 1,
        "unitPrice": 1000
      }
    ]
  }
}
```

**Example Response:**

```json
{
  "data": {
    "createSalesInvoice": {
      "id": "a4e6d9b2-2e41-4df0-9f83-c4d1a553fe24" 
    }
  }
}
```

> **Important:** Make sure to store the ID of this advance invoice (`a4e6d9b2-2e41-4df0-9f83-c4d1a553fe24` in this example), as it will be required later to manage the deduction of the advance payment in the final invoice and establish the link between the advance invoice and the final invoice.

### Example: Creating an Invoice with Advance Payment Deduction

Here, the last line deducts the advance payment using the ID of the advance payment product and references the advance invoice ID via `originId`.

> **Important:** The ID `f50af716-34ff-4c1b-8e65-dd08220a056b` for the advance payment line must strictly be the ID of the advance payment product retrieved by filtering on the product with the `CONCEPT` category. The `originId` field must be filled with the ID of the advance invoice previously created and stored.

**GraphQL Mutation:**

```graphql
mutation ($values: SalesInvoiceCreateGLDtoInput!) { 
  createSalesInvoice(input: $values) {
    id
  }
}
```

**GraphQL Variables:**

```json
{
  "values": {
    "customerId": "3ec1023d-96b4-4a9e-b536-2f8951d63ba0",
    "documentDate": "2025-02-10",
    "lines": [
      {
        "productId": "f50af716-3200-4c1b-8e65-dd08220a056b",
        "totalQuantity": 50,
        "unitPrice": 11,
        "firstDiscount": 5
      },
      {
        "productId": "f50af716-3412-4c1b-8e65-dd08220a056b",
        "totalQuantity": 4,
        "unitPrice": 22,
        "firstDiscount": 0
      },
      {
        "productId": "f50af716-34ff-4c1b-8e65-dd08220a056b",
        "productName": "Advance payment for Quote 0078",
        "totalQuantity": -1,
        "unitPrice": 1000,
        "originId": "a4e6d9b2-2e41-4df0-9f83-c4d1a553fe24"
      }
    ]
  }
}
```

---

## Invoice with Online Payment

### Description

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| canGeneratePaymentLink | Boolean | Required | Indicates whether the invoice should generate an online payment link |
| invoiceEmail | String | Required | Email address used when sending the invoice with the online payment link |

**GraphQL Mutation:**

```graphql
mutation ($values: SalesInvoiceCreateGLDtoInput!) {
  createSalesInvoice(input: $values) {
    id
    operationalNumber
  }
}
```

**GraphQL Variables:**

```json
{
  "values": {
    "customerId": "{{customerId}}",
    "canGeneratePaymentLink": true,
    "invoiceEmail": "[email protected]",
    "lines": [
      {
        "productId": "{{firstProductId}}",
        "totalQuantity": 50,
        "unitPrice": 11,
        "firstDiscount": 5
      },
      {
        "productId": "{{secondProductId}}",
        "productName": "specific name",
        "totalQuantity": 4,
        "unitPrice": 22,
        "firstDiscount": 0
      }
    ],
    "paymentTermLines": [
      {
        "paymentMeanId": "{{onlinePaymentMeanId}}",
        "type": "REMAINING_AMOUNT",
        "value": 0,
        "condition": "DAY_OF_PAYMENT",
        "day": 30
      }
    ]
  }
}
```

### Online Payment Authorization Rules

Online payment is authorized only if:
- The Invoice payment condition uses a Payment Mean with `type = PAYMENT_IN`
- This payment condition is the only payment condition defined
- The Invoice is posted
- `canGeneratePaymentLink = true`

The online payment condition may:
- Be defined by default in the Customer payment terms
- Or be explicitly set during Invoice creation

If multiple payment conditions are present, the online payment link will not be generated.

### Step 3 -- Post the Invoice

The Invoice must be posted before generating the PDF. Only a posted Invoice can generate a valid online payment link.

### Step 4 -- Generate or Send the PDF

Once the Invoice is posted, you can:
- Send it via `DocumentPdfEmail`
- Preview it via `DocumentPdfPreview`

The generated PDF will contain the online payment link, allowing the customer to pay directly.

### Important Behavior

- If `canGeneratePaymentLink = true` but the Invoice does not meet the payment condition rules, no payment link will be included.
- If the Invoice is not posted, no link will be generated.
- The payment link is generated dynamically at PDF generation time.
- The online payment link depends on the Payment Mean configuration with `type = PAYMENT_IN`.
