# Sales Actions — Credit Notes, PDF, Email, Settlements

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/generatecreditnote
> - https://developer.sage.com/sageactive/resources/previewpdf
> - https://developer.sage.com/sageactive/resources/pdfemail
> - https://developer.sage.com/sageactive/resources/salesinvoiceopenitems
> - https://developer.sage.com/sageactive/resources/salesopenitemsettlement
> - https://developer.sage.com/sageactive/resources/relatedsalesdocuments
> - https://developer.sage.com/sageactive/resources/linkrelatedsalesdocuments
> - https://developer.sage.com/sageactive/resources/updatedocumentstatus
> - https://developer.sage.com/sageactive/resources/salesdocuments
> - https://developer.sage.com/sageactive/resources/salesdocumenttransformation
> - https://developer.sage.com/sageactive/resources/documentlines
> - https://developer.sage.com/sageactive/resources/saleskpis

## Sales Documents Overview

This section outlines various types of sales documents that are essential to the sales process. These include:

- Quotes
- Orders
- Delivery Notes
- Invoices

Each type of document holds specific information and serves unique purposes.

### Shared Characteristics and Unique Fields in Sales Documents

While Quotes, Orders, Delivery Notes, and Invoices are distinct types of sales documents, they share a significant amount of common structure. The majority of fields and attributes are identical across these documents, promoting a consistent user experience and facilitating document management.

- **Common Fields:** All four types of documents include basic information such as customer details, product listings, quantities, and pricing.
- **Unique Fields:** However, each document type also has specific fields and attributes that are unique to its role in the sales process. For example, a Delivery Note may have fields related to shipment tracking, while an Invoice includes fields for payment terms and due dates.

This architecture simplifies the sales workflow, while also providing the necessary granularity where required, for each stage of the sales process.

### Sales Quotes

Both known as Sales quotes in the API context and in the product interface. This resource serves as the blueprint for creating, modifying, and tracking sales quotes within the sales pipeline. It leverages standard and customized data fields to accurately represent quotes for customers, which are crucial for managing and closing sales opportunities.

Note that it is possible to enter quotes for leads by providing the lead's identification information within the context of the quote. This avoids the need to always create a customer in advance. However, this possibility is limited to the quote; as soon as a quote is won and generates an order, a customer becomes mandatory.

### Sales Orders

Both known as Sales orders in the API context and in the product interface. This resource serves as the comprehensive record for managing customer orders within the order-to-cash process. Utilizing the standardized and custom data fields, it captures detailed information such as customer codes, discounts, and product lines, which are essential for fulfilling customer orders.

### Sales Delivery Notes

Both known as Sales delivery notes in the API context and in the product interface. This resource is the backbone for organizing and managing the shipping and handling details associated with sales orders. With the flexibility to incorporate both standard and customized fields, Sales Delivery Notes offer granular visibility into the logistical aspects of the order-to-cash process. This is critical for ensuring timely and accurate deliveries to customers.

### Sales Invoices

Both known as Sales Invoices in the API context and in the product interface. This resource is pivotal for invoicing customers after order fulfillment and serves as a legally binding request for payment. The Sales Invoices entity leverages both standard and custom fields, allowing businesses to create detailed invoices that include necessary information like customer codes, discounts, product lines, and tax details. This ensures compliance and contributes to a smoother accounts receivable process.

### Invoice Workflow

1. **Create the Invoice and Optionally View or Modify It:** Start by creating a sales invoice using the SalesInvoice service. Once created, you can view and make any necessary modifications to the invoice before finalizing it.
2. **Close the Invoice to Prevent Further Modifications:** Use the CloseSalesInvoice service to confirm the invoice. This action locks the invoice, ensuring that its content cannot be changed. This step is essential for securing the invoice details before posting.
3. **Post the Invoice to Generate Accounting Entries:** After closing, proceed with posting the invoice using the PostSalesInvoice service. Posting the invoice will generate the necessary accounting entries, making the invoice part of the company's financial records.
4. **View the Invoice's Payment Due Dates:** Check the payment due dates and related information using the SalesInvoiceOpenItems service. This allows you to see when payments are expected and track outstanding amounts for the invoice.
5. **Apply Partial or Full Payments to the Invoice:** Manage the payments on the invoice by using the SalesOpenItemSettlement service. You can apply both partial and full payments, keeping track of amounts paid and any remaining balances.
6. **Generate a Credit Note if Necessary:** In case of returns, refunds, or corrections, you can issue a credit note related to the invoice using the generateCreditNote service.

### Units of Measurement

Represents the various units of measurement known as UnitsOfMeasurements in the API context and used for defining product quantities in the product interface. These values are standardized to ensure consistency and accuracy across all calculations. Each unit of measurement includes metadata such as Abbreviation, Number of Decimals, and Name, providing detailed descriptions for quantities.

The Unit of measurement Sales Setup parameter allows customization of these units within the product interface to define, for each unit, whether it will be used and, if so, for which Product Categories it applies.

---

## some Filter vs. Direct Line Filtering

### Context

When querying documents (such as Sales Quotes, Sales Orders, Sales Delivery Notes, and Sales Invoices) in GraphQL, there are two primary methods to filter based on line items:

- Using a `some` filter within the document query
- Querying document lines directly with a filter

Each method has different use cases and produces distinct results.

### Differences Between the Two Approaches

- **Using a `some` filter** in document queries allows you to retrieve documents that have at least one line item meeting a specific condition. However, all line items associated with those documents are returned, regardless of whether they meet the filter criteria.
- **Directly querying document lines** applies the filter at the line item level, meaning only the lines that satisfy the specified condition are returned, independent of other lines in the document.

### Example: Using some Filter in Document Query

Retrieve all invoices between two dates where the product CLMENWATCH has been sold.

In this example, all salesInvoices that have at least one line selling the product CLMENWATCH within the specified date range will be retrieved. Note that all lines of these invoices are returned, even if some do not contain the product CLMENWATCH.

```graphql
query {
  salesInvoices (
    order: { documentDate: DESC },
    where: {
      and: [
        { documentDate: { gte: "2024-01-01" } },
        { documentDate: { lte: "2024-12-31" } },
        { lines: { some: { 
          productCode : {eq :"CLMENWATCH"}
        }}}
      ]
    }
  ) {
    edges {
      node {
        id
        socialName
        operationalNumber
        creationDate
        lines {
          productCode
          productName
          totalQuantity
        }
      }
    }
  }
}
```

**Example Result:**

| Invoice ID | Social Name | Operational Number | Creation Date | Product Code | Product Name | Quantity |
|---|---|---|---|---|---|---|
| INV12345 | ABC Corp | 123456 | 2024-03-10 | CLMENWATCH | Classic Men's Watch | 10 |
| | | | | SILVCHAIN | Silver Chain | 5 |
| | | | | GLDRING | Gold Ring | 2 |
| INV12346 | XYZ Ltd | 789012 | 2024-03-12 | DIAMRINGS | Diamond Rings | 7 |
| | | | | PRLNECKLACE | Pearl Necklace | 3 |
| | | | | CLMENWATCH | Classic Men's Watch | 8 |
| | | | | SAPHCHOKER | Sapphire Choker | 4 |

### Example: Direct Line Filtering

Retrieve quantities and sales prices of the product CLMENWATCH sold between two dates.

In this example, only the lines where the product CLMENWATCH is sold within the specified date range are retrieved. The query returns relevant line details, such as quantity and unit price, along with the associated sales invoice information.

```graphql
query {
  salesInvoiceLines(
    order: { salesInvoice: { documentDate: DESC } },
    where: {
      and: [
        { salesInvoice: { documentDate: { gte: "2024-01-01" } } },
        { salesInvoice: { documentDate: { lte: "2024-12-31" } } },
        { productCode: { eq: "CLMENWATCH" } }
      ]
    }
  ) {
    edges {
      node {
        salesInvoice {
          id
          operationalNumber
          documentDate
        }
        productCode
        productName
        totalQuantity
        unitPrice
        totalNet
      }
    }
  }
}
```

**Example Result:**

| Invoice ID | Social Name | Operational Number | Creation Date | Product Code | Product Name | Total Quantity |
|---|---|---|---|---|---|---|
| INV12345 | ABC Corp | 123456 | 2024-03-10 | CLMENWATCH | Classic Men's Watch | 10 |
| INV12346 | XYZ Ltd | 789012 | 2024-03-12 | CLMENWATCH | Classic Men's Watch | 8 |

### Recommendation

Select the approach that best fits your data retrieval requirements:

- Use document queries with a `some` filter when you need to retrieve entire documents while still considering all their associated line items.
- Use direct document line filtering when focusing solely on line items that meet certain criteria, without including the rest of the document's lines.

> **Caution:** Understand that using the `some` filter will return the entire document with all its lines, not just the ones that match the filter condition. This could lead to retrieving more data than necessary, which might impact performance or require additional processing.

---

## Update Document Status

### Sales Quotes

To update the status of a sales quote, use the `updateSalesQuote` mutation and specify the desired status for the `updateDocumentStatus` field.

| HTTP | Operation | Type | Object | DTO |
|---|---|---|---|---|
| POST | Update | Mutation | updateSalesQuote | SalesQuoteUpdateGLDtoInput |

#### updateDocumentStatus Fields

| Field | Type | Description | Length |
|---|---|---|---|
| updateDocumentStatus | `CANCEL_DECLINE` / `DECLINE` / `CLOSE` | Allows changing the status of the quote | |
| declinedReason | String | Indicates the reason why a quote was declined | 100 |

**updateDocumentStatus Options:**

- **DECLINE:** Marks a quote as declined. Optionally, use the `declinedReason` field to specify the reason for declining.
- **CANCEL_DECLINE:** Reverts a previously declined quote back to a Pending state.
- **CLOSE:** Accepts and validates the quote. Once a quote is marked as Closed, its status cannot be changed further.

> **Note:** To determine the current status of the document, you must read the value of the `status` field. The `updateDocumentStatus` field is only used for updating the document's status.

#### Status Values

| Field | Type | Description |
|---|---|---|
| status | String | `Pending` / `Declined` / `Closed` |

### Sales Orders

To update the status of a sales order, use the `updateSalesOrder` mutation and specify the desired status for the `updateDocumentStatus` field.

| HTTP | Operation | Type | Object | DTO |
|---|---|---|---|---|
| POST | Update | Mutation | updateSalesOrder | SalesOrderUpdateGLDtoInput |

#### updateDocumentStatus Fields

| Field | Type | Description |
|---|---|---|
| updateDocumentStatus | `CLOSE` | Allows changing the status of the sales order |

**updateDocumentStatus Options:**

- **CLOSE:** Finalizes the sales order. Once closed, no further updates to the status are allowed.

> **Note:** To determine the current status of the document, you must read the value of the `status` field. The `updateDocumentStatus` field is only used for updating the document's status.

#### Status Values

| Field | Type | Description |
|---|---|---|
| status | String | `Pending` / `Closed` |

### Sales Delivery Notes

To update the status of a sales delivery note, use the `updateSalesDeliveryNote` mutation and specify the desired status for the `updateDocumentStatus` field.

| HTTP | Operation | Type | Object | DTO |
|---|---|---|---|---|
| POST | Update | Mutation | updateSalesDeliveryNote | SalesDeliveryNoteUpdateGLDtoInput |

#### updateDocumentStatus Fields

| Field | Type | Description |
|---|---|---|
| updateDocumentStatus | `CLOSE` | Allows changing the status of the delivery note |

**updateDocumentStatus Options:**

- **CLOSE:** Marks the delivery note as completed and finalized.

> **Note:** To determine the current status of the document, you must read the value of the `status` field. The `updateDocumentStatus` field is only used for updating the document's status.

#### Status Values

| Field | Type | Description |
|---|---|---|
| status | String | `Pending` / `Closed` |

### Sales Invoices

For sales invoices, status updates follow a different logic and are handled via business actions rather than the `updateDocumentStatus` field.

| Action | Description |
|---|---|
| closeSalesInvoice | Finalizes the invoice, indicating it is no longer editable |
| postSalesInvoice | Posts the invoice to the accounting system for processing |

> **Note:** To determine the current status of the document, you must read the value of the `status` field.

#### Status Values

| Field | Type | Description |
|---|---|---|
| status | String | `Pending` / `Closed` / `Posted` / `PartiallyCollected` / `Collected` |

### Recommendation

Ensure you select the appropriate status update or action based on the type of sales document and its current workflow. Follow the constraints for each document type to maintain data consistency and compliance with business rules.

---

## Sales Document Transformation

### Origin Fields for Line-Level Transformation

When creating a sales document from another (e.g. Quote to Order, Order to Invoice), you can specify the origin at line level using the following fields:

| Field | Type | Required | Notes |
|---|---|---|---|
| originType | `DELIVERY_NOTE` / `ORDER` / `QUOTE` | Yes (mutation only) | Type of the source document |
| originId | UUID | Yes (mutation only) | Identifier of the source document |
| originLineId | UUID | Yes (mutation only) | Identifier of the source document line |

Because these fields are available at line level, a target document can be created from multiple source documents. This enables advanced scenarios such as:

- Creating one Delivery Note from multiple Sales Orders
- Creating one Invoice from multiple Delivery Notes or from multiple Quotes or Orders

Each line keeps its own origin, ensuring full traceability and correct quantity management.

These fields allow the API to automatically:

- Create a RelatedSalesDocument link
- Reduce the remaining quantity on the origin document line
- Ensure consistency of the sales flow

### Example 1: Transform a SalesQuote into a SalesOrder

**GraphQL mutation:**

```graphql
mutation ($values: SalesOrderCreateGLDtoInput!) {
  createSalesOrder(input: $values) {
    id
    operationalNumber
  }
}
```

**Variables:**

```json
{
  "values": {
    "customerId": "{{customerId}}",
    "lines": [
      {
        "productId": "{{productId_1}}",
        "totalQuantity": 2,
        "unitPrice": 11,
        "originType": "QUOTE",
        "originId": "{{salesQuoteId}}",
        "originLineId": "{{salesQuoteLineId}}"
      }
    ]
  }
}
```

### Example 2: Transform multiple SalesOrders into one DeliveryNote

**GraphQL mutation:**

```graphql
mutation ($values: SalesDeliveryNoteCreateGLDtoInput!) {
  createSalesDeliveryNote(input: $values) {
    id
    operationalNumber
  }
}
```

**Variables:**

```json
{
  "values": {
    "customerId": "{{customerId}}",
    "lines": [
      {
        "productId": "{{productId_1}}",
        "totalQuantity": 5,
        "unitPrice": 20,
        "originType": "ORDER",
        "originId": "{{salesOrderId_1}}",
        "originLineId": "{{salesOrderLineId_1}}"
      },
      {
        "productId": "{{productId_2}}",
        "totalQuantity": 3,
        "unitPrice": 18,
        "originType": "ORDER",
        "originId": "{{salesOrderId_2}}",
        "originLineId": "{{salesOrderLineId_2}}"
      }
    ]
  }
}
```

### Example 3: Transform a DeliveryNote into an Invoice

**GraphQL mutation:**

```graphql
mutation ($values: SalesInvoiceCreateGLDtoInput!) {
  createSalesInvoice(input: $values) {
    id
    operationalNumber
  }
}
```

**Variables:**

```json
{
  "values": {
    "customerId": "{{customerId}}",
    "lines": [
      {
        "productId": "{{productId_1}}",
        "totalQuantity": 5,
        "unitPrice": 20,
        "originType": "DELIVERY_NOTE",
        "originId": "{{deliveryNoteId}}",
        "originLineId": "{{deliveryNoteLineId}}"
      }
    ]
  }
}
```

### Important Behavior

When using `originType`, `originId`, and `originLineId` on lines:

- The API validates that the transformation path is allowed
- The quantity may exceed the remaining quantity of the origin line
- However, the origin document must be in Pending status in order to be referenced by another document
- Multiple target documents can be created from the same origin document, as long as the origin lines are not completed
- For Invoice creation from Delivery Notes, there is no notion of `pendingQuantity` on Delivery Note lines. As soon as a Delivery Note line is used, it becomes Completed and cannot be used again for another Invoice
- The relationship is visible through the RelatedSalesDocument resource

### Recommendation

Always use these fields on lines when creating a document that originates from another sales document. Failing to do so will create a standalone document with no traceability and no quantity control against the source document.

---

## Generate Credit Note

| HTTP | Operation | Type | Object | DTO |
|---|---|---|---|---|
| POST | Create | Action (Mutation) | generateCreditNote | GenerateCreditNoteGLDtoInput |

### Description

The GenerateCreditNote service allows you to create a full or partial credit note for an invoice that must be in Closed or Posted status directly through the API. This service facilitates the automated creation of credit notes to adjust or cancel issued invoices.

### Functionality

- **Full Credit Note:** If only the invoice ID is provided, a credit note for the total amount of the invoice is generated.
- **Partial Credit Note:** If a `lines` block is provided in the request, a partial credit note can be generated, specifying the affected products and quantities to be deducted. This feature is designed to handle credit notes for product returns or service cancellations, rather than financial credit notes related to price reductions. For this reason, it is sufficient to specify the products and quantities without requiring the unit price to be mentioned.
- **Invoice Status Requirement:** Only invoices in Closed or Posted status can be processed.

### Input Parameters

- **id** (Mandatory): The unique identifier of the invoice that requires a credit note.
- **lines** (Optional): An array of specific invoice lines for which a partial credit note is generated.

### Response

The service returns a `GenerateCreditNoteOutputGLDto` object containing:

- **id**: The unique identifier of the credit note created.

### Case 1: Full Credit Note

If only the invoice ID is provided, a credit note is generated for the entire invoice amount.

**GraphQL Query:**

```graphql
mutation($input: GenerateCreditNoteGLDtoInput!) {
  generateCreditNote(input: $input) {
    id
  }
}
```

**GraphQL Variables:**

```json
{
  "input": {
    "id": "7af9f050-4a34-4a0b-a1af-30fe00a38abc"
  }
}
```

**Example Response:**

```json
{
  "data": {
    "generateCreditNote": {
      "id": "0e1987c3-f762-4906-8b26-42e4a4c36079"
    }
  }
}
```

### Case 2: Partial Credit Note

If a `lines` block is provided, a partial credit note is generated, specifying which products, quantities, and amounts are affected.

**GraphQL Query:**

```graphql
mutation($input: GenerateCreditNoteGLDtoInput!) {
  generateCreditNote(input: $input) {
    id
  }
}
```

**GraphQL Variables:**

```json
{
  "input": {
    "id": "7af9f050-4a34-4a0b-a1af-30fe00a38abc",
    "lines": [
      {
        "productId": "e9d05ca3-79f8-4f50-ad2e-14dfa3d5604e",
        "quantity": 1.00
      },
      {
        "productId": "a3f29b17-5c2d-4d8e-91f1-9b8dcf1a8d3c",
        "quantity": 2.50
      }
    ]
  }
}
```

**Example Response:**

```json
{
  "data": {
    "generateCreditNote": {
      "id": "3b12a8d2-9b4d-4c6d-8e47-10f6e2d9e7fa"
    }
  }
}
```

### generateCreditNote Input Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | Yes | Unique identifier of the invoice. The invoice must be in Closed or Posted status. |
| lines[] | Array | No | List of specific invoice lines for partial credit. If not provided, the credit note will be full (entire invoice canceled). |
| operationalNumberPresetText | OperationalNumberPresetText | No | Operational number preset text used for the generated credit note (added 2026-05) |
| operationalNumberPresetTextId | UUID | No | Operational number preset text identifier for partial credit (added 2026-05) |
| outputDocumentDate | DateTime | No | Output document date for partial credit notes (added 2026-06) |

### generateCreditNote/lines Input Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| productId | UUID | Yes | Unique identifier of the product being credited |
| quantity | Decimal | Yes | Quantity of the product to be credited. Cannot exceed the originally invoiced quantity minus any previous credit notes. |
| unitPrice | Decimal | No | Unit price is automatically retrieved from the original invoice line. Can be specified if the invoice contains multiple instances of the same product with different prices. |

### generateCreditNote Response

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | - | Unique identifier of the credit note created |

---

## Document PDF Preview

| HTTP | Operation | Type | Object | DTO |
|---|---|---|---|---|
| POST | Read | Action (Query) | documentPdfPreview | DocumentPdfPreviewGLDtoInput |

### Description

The DocumentPdfPreview service allows you to preview documents in PDF format. This service generates a binary representation of the PDF. This binary data, often referred to as a blob, can easily be displayed in a PDF viewer embedded within a browser tab.

### Functionality

- **Document Retrieval:** Fetches a binary representation of the document in PDF format.
- **Supports Different Document Types:** Able to generate previews for various document types such as sales quotes, orders, delivery notes, and invoices.

### Required Parameters

- **type**: Type of the document for which the preview is requested.
- **id**: The unique identifier of the document for which the preview is being generated.

### Response

The service returns a `DocumentPdfPreviewOutputGLDto` object containing:

- **type**: The type of the document previewed.
- **id**: The unique identifier of the document.
- **pdfPreview**: A string representing the binary content of the PDF.

### How to Use

**GraphQL Query:**

```graphql
query($input: DocumentPdfPreviewGLDtoInput!) {
  documentPdfPreview(input: $input) {
    type
    id
    pdfPreview
  }
}
```

**GraphQL Variables:**

```json
{
  "input": {
    "type": "SALES_INVOICE",
    "id": "{invoiceId}"
  }
}
```

**Example Response:**

```json
{
  "data": {
    "documentPdfPreview": {
      "type": "SALES_INVOICE",
      "id": "0e1987c3-e712-4906-8b26-42e4a4c36079",
      "pdfPreview": "JVBERi0xLjcKJYGBgYEKCjEwIDAgb2JqCjw8Ci9GaWx0ZXIgL0ZsY..."
    }
  }
}
```

### documentPdfPreview Input Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| type | `SALES_DELIVERY_NOTE` / `SALES_INVOICE` / `SALES_ORDER` / `SALES_QUOTE` | Yes | Type of the document |
| id | UUID | Yes | Unique identifier of the document |

### documentPdfPreview Response

| Field | Type | Required | Notes |
|---|---|---|---|
| type | `SALES_DELIVERY_NOTE` / `SALES_INVOICE` / `SALES_ORDER` / `SALES_QUOTE` | - | Type of the document previewed |
| id | UUID | - | Unique identifier of the document |
| pdfPreview | String | - | Base64 encoded string representing the PDF file. Can be converted to a binary file and viewed in a PDF reader. |

### JavaScript Sample to Display the PDF in a Browser Tab

```javascript
let pdfBlob = fetch(base64Pdf).then(res => res.blob());
pdfBlob.then(blob => {
});
```

---

## Document PDF Email

| HTTP | Operation | Type | Object | DTO |
|---|---|---|---|---|
| POST | Read | Action (Query) | documentPdfEmail | DocumentPdfEmailGLDtoInput |

### Description

The DocumentPdfEmail service allows you to send sales documents in PDF format via email directly from the API. This service automates the process of emailing PDF documents to specified recipients, including CC and BCC options. Email templates can be used as a source of information to prepare the email content.

> **Note:** The DocumentPdfEmail service does not automatically apply any template. The client application must first read the appropriate template and copy its content into the email fields.

### Functionality

- **Email Sending:** Automates sending of documents in PDF format.
- **Supports Different Document Types:** Able to send email for various document types such as sales quotes, orders, delivery notes, and invoices.
- **Customizable Email Details:** Allows setting of sender, subject, and body content for the emails.

### Input Parameters

**Document Details:**

- **type** (Mandatory): Type of the document for which the PDF is requested to be emailed (a quote, an order, a delivery note, an invoice).
- **id** (Mandatory): The unique identifier of the document.

**Email Addresses (Mandatory):**

- **emailFrom**: The email address of the currently logged-in user, from which the email will be sent.
- **emailTo**: The primary recipient's email address.
- **emailCc**: Email addresses for CC recipients.
- **emailBcc**: Email addresses for BCC recipients (optional).
- **sendEmailCopy**: Boolean indicating whether to send a copy of the email to the sender.

**Email Content (Mandatory):**

- **emailSubject**: The subject of the email.
- **emailMessage**: The body content of the email, can be in HTML format. To manage line breaks use `<br>`.

### Response

The service returns a `DocumentPdfEmailOutputGLDto` object containing:

- **type**: Type of the document for which the PDF is requested to be emailed.
- **id**: The unique identifier of the document.

### How to Use

**GraphQL Query:**

```graphql
query($input: DocumentPdfEmailGLDtoInput!) {
  documentPdfEmail(input: $input) {
    type
    id
  }
}
```

**GraphQL Variables:**

```json
{
  "input": {
    "type": "SALES_INVOICE",
    "id": "7af9f050-4a34-4a0b-a1af-30fe00a38abc",
    "emailFrom": "[email protected]",
    "emailTo": "[email protected]",
    "emailCc": "[email protected]",
    "sendEmailCopy": false,
    "emailSubject": "PDF document attachment",
    "emailMessage": "<b>Thank you</b> for your business.<br>Please find attached the document related to your recent transactions.<br>Let us know if you have any questions.<br>Kind regards"
  }
}
```

**Example Response:**

```json
{
  "data": {
    "documentPdfEmail": {
      "type": "SALES_INVOICE",
      "id": "0e1987c3-1f23-4906-8b26-42e4a4c36079"
    }
  }
}
```

### documentPdfEmail Input Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| type | `SALES_DELIVERY_NOTE` / `SALES_INVOICE` / `SALES_ORDER` / `SALES_QUOTE` | Yes | Type of the document |
| id | UUID | Yes | Unique identifier of the document |
| emailFrom | String | Yes | Sender's email address. Can be retrieved from `userProfile` under `authenticationEmail`. |
| emailTo | String | Yes | Primary recipient's email address |
| emailCc | String | No | CC recipient's email addresses |
| emailBcc | String | No | BCC recipient email addresses (added 2026-05) |
| sendEmailCopy | Boolean | No | Whether to send a copy to the sender |
| emailSubject | String | No | Subject of the email |
| emailMessage | String | No | Body content of the email. Supports HTML format; use `<br>` for line breaks. |

> **Using Email Templates:** The API does not automatically apply an email template when sending a document. If you want to use an email template, you must retrieve it using the EmailTemplate resource, then copy the template fields (subject, message, cc, bcc, etc.) into the DocumentPdfEmail input.

### documentPdfEmail Response

| Field | Type | Required | Notes |
|---|---|---|---|
| type | `SALES_DELIVERY_NOTE` / `SALES_INVOICE` / `SALES_ORDER` / `SALES_QUOTE` | - | Type of the document sent |
| id | UUID | - | Unique identifier of the document |

---

## Sales Invoice Open Items

| HTTP | Operation | Type | Object |
|---|---|---|---|
| POST | Read | Query | salesInvoiceOpenItems (filtered by id) |
| POST | List | Query | salesInvoiceOpenItems |

### Description

An Open Item is a financial transaction that allows you to know if it has been fully settled or paid. Each open item includes detailed information such as the total amount, accumulated amount paid, and the payment due date. For a single invoice, there can be as many open items as there are different payment installments, which is why openItems is structured as an array to accommodate multiple entries per invoice.

The payment status provides insight into the progress of settling each item. Additionally, the identifier of the payment method used is provided to track the settlement method.

### Fields

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | System | Auto-assigned identifier |
| creationDate | DateTime | System | Creation date |
| modificationDate | DateTime | System | Modification date |
| amount | Decimal | - | Total amount of the item |
| paidAmountAccumulated | Decimal | - | Accumulated amount that has been paid |
| dueDate | DateTime | - | Due date for the payment (only date value is considered, time is ignored) |
| status | `NOT_SPECIFIED` / `COLLECTED` / `PAID` / `PARTIAL` | - | Status of the payment |
| isAdvanceInvoicePayment | Boolean | - | If the invoice is an Advance Invoice Payment |
| paymentMeanId | UUID | - | Identifier of the payment method used (via DATALOADER PaymentMean) |
| salesInvoiceId | UUID | - | Identifier of the associated sales invoice (via DATALOADER SalesInvoice) |

### Status Values

- **NOT_SPECIFIED:** No specific payment status has been detailed.
- **COLLECTED:** Payment has been fully collected from customers.
- **PAID:** Not used for Customers.
- **PARTIAL:** Payment for the item has been made partially.

---

## Sales Open Item Settlement

| HTTP | Operation | Type | Object | DTO |
|---|---|---|---|---|
| POST | Create | Action (Mutation) | salesOpenItemSettlement | SalesOpenItemSettlementGLDtoInput |

### Description

The SalesOpenItemSettlement service is designed to manage the settlement of open items in financial accounts, allowing users to adjust payments against outstanding invoices partially or fully. The purpose of this action is to target open items that have not yet been fully paid (status = NOT_SPECIFIED or PARTIAL) related to a sales invoice.

A total or partial amount for each due date can be specified. The action will then create a bank entry and modify the open items to align them with the elements mentioned in the action.

### How to Retrieve the IDs of Open Items

- **Retrieving open items from accounting entries:** This method is used when sales invoices have not been recorded in Sage Active, and only the accounting entries related to these invoices have been entered or imported. Open items are accessible via `accountingEntries/accountingEntryLines/accountingEntryInvoice/openItems`.
- **Retrieving open items directly from sales invoices:** This approach is applicable when sales invoices are available in Sage Active. It allows for direct access to open items without going through the accounting entries. Use `salesOpenItemSettlement` for direct retrieval.

### Functionality

- **Settlement of Open Items:** Allows the specification and settlement of open items, adjusting the financial records accordingly.
- **Supports Partial and Full Payments:** Users can specify either partial or full payment amounts for each open item.
- **Automated Financial Entry Creation:** Automatically creates a financial entry in the accounting ledger for global settlement made.

### Input Parameters

**Settlement Details:**

- **entryDate** (Mandatory): The date of the entry for the settlement.
- **paymentMethodId** (Mandatory): The identifier for the bank account or cash where the settlement will be deposited.
- **description** (Optional): Description of the settlement action.
- **documentNumber** (Optional): A reference number for the settlement document.
- **thirdPartyId** (Mandatory): The identifier of the third party involved in the transaction.

**Open Items (Mandatory):**

- **salesOpenItemLinkagePaidAmounts**: List of open items and the amounts paid.

### Response

The service can return the following fields:

- **accountingEntryId**: Unique identifier of the created accounting entry.
- **accountingEntryNumber**: Sequential number of the accounting entry.

These fields confirm that the sales invoice has been successfully posted.

### How to Use

**GraphQL Mutation:**

```graphql
mutation($input: SalesOpenItemSettlementGLDtoInput!) {
  salesOpenItemSettlement(input: $input) {
    accountingEntryId
    accountingEntryNumber
  }
}
```

**GraphQL Variables:**

```json
{
  "input": {
    "entryDate": "2024-01-01",
    "paymentMethodId": "815c1835-90dd-4e62-8504-79a3f97fac67",
    "description": "Full settlement of Invoice IN0022",
    "documentNumber": "IN00022",
    "thirdPartyId": "815c1835-9cb5-4e62-8504-79a3f97fac67",
    "salesOpenItemLinkagePaidAmounts": [
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

**Example Response:**

```json
{
  "data": {
    "salesOpenItemSettlement": {
      "accountingEntryId": "0e1987c3-ed48-4906-8b26-42e4a4c36079",
      "accountingEntryNumber": 818
    }
  }
}
```

### salesOpenItemSettlement Input Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| entryDate | DateTime | Yes | Date of the entry |
| paymentMethodId | UUID | Yes | Bank Account and cash Id |
| description | String | No | Description of the settlement action |
| documentNumber | String | No | Reference number for the document |
| thirdPartyId | UUID | Yes | Third party involved |
| salesOpenItemLinkagePaidAmounts[] | Array | Yes | List of open items and the paid amounts |

### salesOpenItemSettlement/salesOpenItemLinkagePaidAmount

| Field | Type | Required | Notes |
|---|---|---|---|
| openItemId | UUID | Yes | Unique identifier of the open item to settle |
| paidAmount | Decimal | Yes | Amount paid for the specified open item |

### salesOpenItemSettlement Response

| Field | Type | Required | Notes |
|---|---|---|---|
| accountingEntryId | UUID | - | Unique identifier of the accounting entry created |
| accountingEntryNumber | Int | - | Number assigned to the accounting entry |

---

## Related Sales Documents

| HTTP | Operation | Type | Object |
|---|---|---|---|
| POST | Read | Query | relatedSalesDocuments (filtered by id) |
| POST | List | Query | relatedSalesDocuments |

### Description

Related sales documents, known as RelatedSalesDocument in the API context and Related documents in the product interface. This resource is used to represent relationships between sales documents (for example, when a quote is converted into an order or an invoice). It stores both the link (originId) and the type of the origin document (originDocumentType), enabling you to navigate between related sales flows.

Unlike the main SalesDocument resources (quotes, orders, invoices), this entity focuses on the relationship itself rather than the transactional content.

### Allowed Transformations Between Sales Documents

| Transform from / to | Quote | Order | DeliveryNote | Invoice | Advance Invoice |
|---|---|---|---|---|---|
| Quote | - | Yes | Yes | Yes | Yes |
| Order | - | - | Yes | Yes | Yes |
| DeliveryNote | - | - | - | Yes | Yes |
| Invoice | - | - | - | - | Yes |

### Example 1: Query Related Documents for a Quote

**GraphQL Query:**

```graphql
query ($quoteId: UUID!) {
  relatedSalesDocuments(
    where: {
      or: [
        { documentId: { eq: $quoteId } }
        { originId: { eq: $quoteId } }
      ]
    }
    order: { creationDate: ASC }
  ) {
    edges {
      node {
        documentId
        documentType
        operationalNumber
        documentDate
        originId
        originDocumentType
        status
        totalLiquid
      }
    }
  }
}
```

**Variables:**

```json
{
  "quoteId": "11111111-aaaa-bbbb-cccc-222222222222"
}
```

**Example Response:**

```json
{
  "data": {
    "relatedSalesDocuments": {
      "edges": [
        {
          "node": {
            "documentId": "11111111-aaaa-bbbb-cccc-222222222222",
            "documentType": "QUOTE",
            "operationalNumber": "0011",
            "documentDate": "2025-10-05",
            "originId": null,
            "originDocumentType": null,
            "status": "Draft",
            "totalLiquid": 4620
          }
        },
        {
          "node": {
            "documentId": "33333333-aaaa-bbbb-cccc-444444444444",
            "documentType": "ORDER",
            "operationalNumber": "0020",
            "documentDate": "2025-10-15",
            "originId": "11111111-aaaa-bbbb-cccc-222222222222",
            "originDocumentType": "QUOTE",
            "status": "Draft",
            "totalLiquid": 300
          }
        },
        {
          "node": {
            "documentId": "55555555-aaaa-bbbb-cccc-666666666666",
            "documentType": "ORDER",
            "operationalNumber": "0023",
            "documentDate": "2025-10-16",
            "originId": "11111111-aaaa-bbbb-cccc-222222222222",
            "originDocumentType": "QUOTE",
            "status": "Closed",
            "totalLiquid": 600
          }
        }
      ]
    }
  }
}
```

### Example 2: Query the Number of Converted Quotes

**GraphQL Query:**

```graphql
query ($startDate: DateTime!, $endDate: DateTime!) {
  relatedSalesDocuments(
    where: {
      documentDate: { gte: $startDate, lte: $endDate }
      originDocumentType: { eq: QUOTE }
    }
  ) {
    edges {
      node {
        originId
      }
    }
    aggregate(
      aggregateFields: ["originId|DISTINCTCOUNT"]
    ) {
      aggregates {
        value
      }
    }
  }
}
```

**Variables:**

```json
{
  "startDate": "2025-01-01",
  "endDate": "2025-12-31"
}
```

**Example Response:**

```json
{
  "data": {
    "relatedSalesDocuments": {
      "edges": [],
      "aggregate": {
        "aggregates": [
          { "value": 12 }
        ]
      }
    }
  }
}
```

### relatedSalesDocuments Fields

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | System | Auto-assigned identifier |
| creationDate | DateTime | System | Creation date |
| modificationDate | DateTime | System | Modification date |
| documentDate | DateTime | - | Document date |
| documentId | UUID | - | Id of the sales document |
| operationalNumber | String | - | Operational number |
| documentType | `NONE` / `ADVANCE_INVOICE` / `CREDIT_NOTE` / `DELIVERY_NOTE` / `INVOICE` / `ORDER` / `QUOTE` / `SALES_INVOICE_TEMPLATE` | - | Type of sales document |
| status | String | - | `Pending` / `Closed` / `Declined` / `Posted` / `PartiallyCollected` / `Collected` |
| totalLiquid | Decimal | - | Total liquid amount |
| originId | UUID | - | Id of the origin sales document |
| originDocumentType | `NONE` / `ADVANCE_INVOICE` / `CREDIT_NOTE` / `DELIVERY_NOTE` / `INVOICE` / `ORDER` / `QUOTE` / `SALES_INVOICE_TEMPLATE` | - | Type of the origin sales document |

### Status Descriptions

- **Pending:** The document is in progress and can be modified.
- **Closed:** The document can no longer be modified.
- **Declined:** The quote is declined and can no longer be modified.
- **Posted:** (Awaiting collection) The invoice can no longer be modified; it has been transferred to accounting.
- **PartiallyCollected:** (Partially collected) The invoice is partially settled. Payments are pending.
- **Collected:** (Collected) The invoice is fully settled.

---

## Link Related Sales Document

| HTTP | Operation | Type | Object | DTO |
|---|---|---|---|---|
| POST | Create | Action (Mutation) | linkRelatedSalesDocument | LinkRelatedSalesDocumentGLDtoInput |

### Description

The LinkRelatedSalesDocument service allows you to establish a relationship between two existing sales documents, when the transformation is allowed (see the transformation table). As soon as a sales document is created, an entry is automatically added in RelatedSalesDocument for it. This action updates the related record by setting the originId with the identifier of the origin document.

### Functionality

- **Relation Creation:** Links a target sales document to its origin sales document.
- **Validation:** Ensures that only authorized transformations are allowed (e.g. Quote to Order, Quote to Invoice).

### Response

The service returns the identifiers of the linked related sales document, along with its updated fields.

### How to Use

**GraphQL Mutation:**

```graphql
mutation($input: LinkRelatedSalesDocumentGLDtoInput!) {
  linkRelatedSalesDocument(input: $input) {
    documentId
    documentType
    originId
    originDocumentType
  }
}
```

**GraphQL Variables:**

```json
{
  "input": {
    "documentId": "33333333-aaaa-bbbb-cccc-444444444444",
    "originId": "11111111-aaaa-bbbb-cccc-222222222222"
  }
}
```

**Example Response:**

```json
{
  "data": {
    "linkRelatedSalesDocument": {
      "documentId": "33333333-aaaa-bbbb-cccc-444444444444",
      "documentType": "ORDER",
      "originId": "11111111-aaaa-bbbb-cccc-222222222222",
      "originDocumentType": "QUOTE"
    }
  }
}
```

### linkRelatedSalesDocument Input Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| documentId | UUID | Yes | Unique identifier of the sales document to link |
| originId | UUID | Yes | Unique identifier of the origin sales document (the source) |

### linkRelatedSalesDocument Response

| Field | Type | Required | Notes |
|---|---|---|---|
| documentId | UUID | - | Id of the target sales document |
| documentType | `NONE` / `ADVANCE_INVOICE` / `CREDIT_NOTE` / `DELIVERY_NOTE` / `INVOICE` / `ORDER` / `QUOTE` / `SALES_INVOICE_TEMPLATE` | - | Type of the target sales document |
| originId | UUID | - | Id of the origin sales document |
| originDocumentType | `NONE` / `ADVANCE_INVOICE` / `CREDIT_NOTE` / `DELIVERY_NOTE` / `INVOICE` / `ORDER` / `QUOTE` / `SALES_INVOICE_TEMPLATE` | - | Type of the origin sales document |

> **Note:** The mutation only succeeds if the transformation between the originDocumentType and documentType is allowed. Otherwise, an error is returned. The logic is consistent with the allowed transformations table.

---

## Sales KPIs

| HTTP | Operation | Type | Object | DTO |
|---|---|---|---|---|
| POST | Read | Action (Query) | salesKpis | SalesKpiInput |

### Description

The Sales KPIs service provides a set of commercial indicators (Key Performance Indicators) that can be retrieved for a given organization. Each KPI corresponds to a standardized calculation based on the organization's sales and related data.

- Provides sales performance indicators such as incoming and outgoing payments, value of open quotes, number of customers, etc.
- Includes KPIs related to quotes conversion through their related sales documents.
- Supports retrieval of one or multiple KPIs in a single call.

### Input Parameters

- **kpiTypes** (Mandatory): One or several values from the enumeration `SalesIndicatorType` (see table below).

### Response

The service returns a structured list of KPIs. Each KPI contains:

- **type**: The KPI identifier (enum).
- **value**: The numeric value of the KPI.
- **currency**: The currency code (e.g. EUR).

### How to Use

**Query:**

```graphql
query ($kpis: [SalesIndicatorType!]!) {
  salesKpis(input: { kpiTypes: $kpis }) {
    type
    value
    currency
  }
}
```

**Variable for a single KPI:**

```json
{
  "kpis": ["MONEY_IN"]
}
```

**Response:**

```json
{
  "data": {
    "salesKpis": [
      {
        "type": "MONEY_IN",
        "value": 79566.8,
        "currency": "EUR"
      }
    ]
  }
}
```

**Variable for multiple KPIs:**

```json
{
  "kpis": ["MONEY_IN", "MONEY_OUT"]
}
```

**Response:**

```json
{
  "data": {
    "salesKpis": [
      {
        "type": "MONEY_IN",
        "value": 79566.8,
        "currency": "EUR"
      },
      {
        "type": "MONEY_OUT",
        "value": 23415.25,
        "currency": "EUR"
      }
    ]
  }
}
```

### Available KPIs

| # | KPI | Description |
|---|---|---|
| 1 | MONEY_IN | Incoming payments (cash received) |
| 2 | MONEY_OUT | Outgoing payments (cash paid) |
| 3 | QUOTES_WITH_RELATED_DOCUMENTS | Quotes linked to subsequent documents |
| 4 | TOTAL_CUSTOMERS | Total number of customers |
| 5 | TOTAL_FROM_OPEN_QUOTES | Total value of open quotes |

**Full KPI Response Example:**

```json
[
  {
    "type": "MONEY_IN",
    "value": 79566.8,
    "currency": "EUR"
  },
  {
    "type": "MONEY_OUT",
    "value": 23415.25,
    "currency": "EUR"
  },
  {
    "type": "QUOTES_WITH_RELATED_DOCUMENTS",
    "value": 0,
    "currency": null
  },
  {
    "type": "TOTAL_CUSTOMERS",
    "value": 340,
    "currency": null
  },
  {
    "type": "TOTAL_FROM_OPEN_QUOTES",
    "value": 48102.94,
    "currency": "EUR"
  }
]
```

### salesKpis Input Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| kpiTypes | `MONEY_IN` / `MONEY_OUT` / `QUOTES_WITH_RELATED_DOCUMENTS` / `TOTAL_CUSTOMERS` / `TOTAL_FROM_OPEN_QUOTES` | Yes | One or more KPI types (array) |

### salesKpis Response

| Field | Type | Required | Notes |
|---|---|---|---|
| type | Enum | - | KPI type (AccountingIndicatorType) |
| value | Decimal | - | Numeric value |
| currency | String | - | Currency code (e.g. EUR). May be null if N/A |
