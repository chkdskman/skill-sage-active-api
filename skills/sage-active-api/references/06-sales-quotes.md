# Sales Quotes — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/salesquotes

## HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | createSalesQuote | SalesQuoteCreateGLDtoInput |
| POST | Update | Mutation | updateSalesQuote | SalesQuoteUpdateGLDtoInput |
| POST | Delete | Mutation | deleteSalesQuote | SalesQuoteDeleteGLDtoInput |
| POST | Read | Query | salesQuotes filtered by id | |
| POST | List | Query | salesQuotes | |
| POST | List | Query | salesQuoteLines | |

### Quick Tips

- It is possible to query either salesQuote or directly from salesQuoteLines.
- Querying salesQuotes allows you to retrieve entire documents with all their associated line items. This is useful when you need the complete context of a document, including all details of the related lines.
- Querying salesQuoteLines directly is beneficial when you want to filter or aggregate specific line item data across multiple documents, such as analyzing product sales, quantities, or prices.

## Description

Both known as Sales quotes in the API context and in the product interface.

This resource serves as the blueprint for creating, modifying, and tracking sales quotes within the sales pipeline. It leverages standard and customized data fields to accurately represent quotes for customers, which are crucial for managing and closing sales opportunities.

Note that it is possible to enter quotes for leads by providing the lead's identification information within the context of the quote. This avoids the need to always create a customer in advance. However, this possibility is limited to the quote; as soon as a quote is won and generates an order, a customer becomes mandatory.

To change the Status of a salesQuote, refer to the global sales documents page, which details the available options depending on the document type.

### Example: Adding a Quote

**GraphQL Mutation:**

```graphql
mutation ($values: SalesQuoteCreateGLDtoInput!) {
  createSalesQuote(input: $values) {
    id
    operationalNumber
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
    "createSalesQuote": {
      "id": "e451aff7-074f-4e72-a6fb-4ea25e9d9c64",
      "operationalNumber": "0027"
    }
  }
}
```

## Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {Current access Token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

## salesQuotes Fields

### Common to All Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system field, cannot be assigned) |
| creationDate | DateTime | System | Creation Date (system field, cannot be assigned) |
| modificationDate | DateTime | System | Modification Date (system field, cannot be assigned) |
| sourceType | SALES / PUBLIC_API | System | Indicates the origin of the salesQuote (SALES=Manual entry) |

### General Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| customer | Customer | No | Fields of Customer (DATALOADER) |
| customerId | UUID | Yes (create only) | ID of the Customer entity. Not mandatory for Quote. |
| documentDate | DateTime | No | Document Date (only date value considered, time ignored) |
| operationalNumber | String (50) | No | Document Number |
| operationalNumberPresetText | OperationalNumberPresetText | No | Fields of OperationalNumberPresetText (DATALOADER, COMING SOON) |
| operationalNumberPresetTextId | UUID | No | ID of the operational number preset text (COMING SOON) |
| status | String (15) | No | Possible values: Pending, Closed, Declined |
| socialName | String (50) | No (create only) | Social Name |
| contactName | String (50) | No | Contact Name |
| contactPhone | String (15) | No | Contact Phone |
| contactEmail | String (50) | No | Contact Email |
| contactJobAreaId | UUID | No | Id of the Contact Job Area |
| countryAcronym | String (2) | No | Country Acronym |

### Quote Lines

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| lines[] | Array | Yes | Document Lines |

### Totals and Discount

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalNet | Decimal | No | Amount after discounts, before taxes |
| totalGross | Decimal | No | Total amount before discounts and taxes |
| totalDiscount | Decimal | No | Sum of all discounts applied |
| totalCustomerDiscount | Decimal | No | Discount specific to the customer |
| totalLiquid | Decimal | No | Final amount due, including taxes |
| totalVat | Decimal | No | Total Value Added Tax (VAT) applied |
| totalVatBase | Decimal | No | Total taxable base before VAT |
| totalFeeSurcharge | Decimal | No | Total equivalence surcharge applied, based on VAT |
| totalVatFee | Decimal | No | VAT amount used as the base for the surcharge |
| discount | Decimal | No | Specific discount applied |
| totalLiquidNoWithholding | Decimal | No | Total amount excluding withholding |
| totalWithholding | Decimal | No | Total withholding amount |

### Notes

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| comments | String (1000) | No | Notes for the customer/recipient of the document |
| remarks | String (1000) | No | Internal notes for the document |
| externalReference | String (20) | No | External customer reference provided to facilitate communication and tracking |

### Customer Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| documentId | String (20) | No | Identification Number |
| documentLanguage | String (20) | No | Language used in sales Document PDF. Values: ENGLISH, FRENCH, GERMAN, SPANISH |
| documentType | DocumentType | No | Fields of DocumentType used for identification purposes (DATALOADER) |
| documentTypeId | UUID | No | Identifier of the document type |
| vatNumber | String (20) | No | VAT Number |
| invoiceEmail | String (50) | No | Invoice Email (NEW) |
| hasEquivalenceSurcharge | Boolean | No | Subject to the equivalence surcharge regime |
| mainAddress | ObjectType | No | Main Address |
| deliveryAddress | ObjectType | No | Delivery Address |
| salesDiscountGroup | SalesDiscountGroup | No | Fields of SalesDiscount (DATALOADER) |
| salesDiscountGroupId | UUID | No | Sales Discount ID |
| salesTariff | SalesTariff | No | Fields of SalesTariff (DATALOADER) |
| salesTariffId | UUID | No | Sales tariff ID |

### Specific to Sales Quote Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| updateDocumentStatus | PENDING / DECLINED / CLOSE | Mutation only | Allows changing the status of the quote |
| declinedReason | String (100) | No | Indicates the reason why a quote was declined |

### Info

- **customerId**: A unique identifier (UUID) for the Customer entity.
- **totalGross**: Represents the total amount before any discounts or taxes are applied. It includes the sum of all item prices at their full value.
- **totalDiscount**: The aggregated discount amount applied to the document, including all types of discounts that reduce the gross total.
- **totalNet**: The total amount after applying discounts but before adding taxes. It represents the taxable base amount.
- **discount**: Represents a specific discount amount applied within the document. This can be a percentage or a fixed amount reduction.
- **totalCustomerDiscount**: A discount specifically applied based on customer-specific conditions, such as loyalty discounts or promotional offers tailored to the customer.
- **totalLiquid**: The total payable amount, which includes taxes and any additional fees. This represents the final amount due.
- **totalVat**: The total Value Added Tax (VAT) calculated on the taxable base. This amount represents the sum of all VAT applied across items in the document.
- **totalVatBase**: The total taxable base amount before VAT is applied. This is the sum of all net amounts subject to VAT.
- **totalLiquidNoWithholding / totalWithholding**: Unused (Spain only). The following fields are specific to the Spanish legislation (IRPF): totalLiquidNoWithholding is the total amount excluding withholding tax (the taxable base before applying IRPF withholding). totalWithholding is the total withholding amount (IRPF) calculated at document level.
- **hasEquivalenceSurcharge / totalFeeSurcharge / totalVatFee**: Unused for the French market. Indicates whether a customer is subject to the equivalence surcharge regime. The equivalence surcharge (totalFeeSurcharge), as applied in Spain, is a form of simplified VAT for retail traders who cannot recover VAT. The rates vary according to the VAT rate applicable to the products (5.2% for 21% VAT, 1.4% for 10% VAT, etc.). The equivalence surcharge is calculated on the amount of general VAT (totalVatFee). Unused for the German market.
- **socialName**: Mandatory for the French market. Not mandatory for the Spanish or German markets.
- **externalReference**: Allows entering an external reference provided by the customer to facilitate communication and tracking.
- **vatNumber**: An EU VAT Number is a unique identifier for tax purposes within the European Union, used primarily for cross-border transactions. It starts with a two-letter country code followed by a series of numbers and sometimes letters, varying by country. This field is not applicable for customers that are not registered in the VIES system. For VIES customers, the format is described at FAQ N 11 https://ec.europa.eu/taxation_customs/vies/#/faq. Mandatory for VIES customers. Not mandatory for Spanish or German markets.
- **invoiceEmail**: Email address used when sending the Invoice PDF through DocumentPdfEmail. This email is required when creating a Sales Invoice and canGeneratePaymentLink is set to true, as it is used for online payment communication. If not provided, the Invoice can still be created, but the online payment email workflow will not be available.
- **documentId**: Identification number, value formats may differ depending on legislationCode. For French: any SIREN of 9 characters or any SIRET of 14 characters (not mandatory). For German: either a Steuer-IdNr with exactly 11 characters or another value ranging from 11 to 14 characters (not mandatory). For Spanish: a valid NIF/DNI/NIE number (mandatory).
- **documentLanguage**: Applies to section titles and column headers in sales documents PDFs.
- **documentTypeId**: This functionality determines the corresponding document type based on the document number provided. This determination is vital for verifying the real existence of a customer, thereby ensuring their authenticity and compliance with regulations.

### Specific to Quotes

- **Status**:
  - Pending: The quote is in progress and can be modified.
  - Closed: The quote can no longer be modified; it has either been transferred to an order or it has been closed.
  - Declined: The quote is declined and can no longer be modified.
- **customerId**: It is not mandatory to link an existing customer in Sage Active to a quote. This allows for sending offers to prospects without the need to create a customer. For this purpose, it is possible to directly specify the contact details of the third party in the quote, which will be specific to the current quote. However, for other types of documents, linking the document to an existing customer is mandatory. Through the Sage Active interface, when generating an order from a quote, if the quote is not associated with a customer, the creation of the customer will be required.

## salesQuotes Lines (salesQuoteLines)

If the query targets salesQuotes, then use lines to get the details of the lines. If the query targets salesQuoteLines, access the following fields directly, including the option to retrieve information on the parent salesQuote.

### Common to All Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system field, cannot be assigned) |
| creationDate | DateTime | System | Date of creation (system field, cannot be assigned) |
| modificationDate | DateTime | System | Last modification date (system field, cannot be assigned) |

### Parent Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| salesQuote | SalesQuote | No | Fields of the parent salesQuotes (DATALOADER) |
| salesQuoteId | UUID | No | salesQuote ID |

### Product Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| product | Product | No | Fields of Product (DATALOADER) |
| productId | UUID | No | Product ID |
| productCode | String (15) | No | Product code |
| productName | String (2500) | No | Product name |
| order | Int | Read-only | Classification of lines from 1 to n |
| unitOfMeasurement | UnitOfMeasurement | No | Fields of Unit of Measurement (DATALOADER) |
| unitOfMeasurementId | UUID | No (default calculated) | Unit of Measurement ID |

### Tax and Price Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalQuantity | Decimal | No | Quantity product |
| unitPrice | Decimal | No | Unit price |
| vatPercentage | Decimal | Read-only | VAT percentage |
| equivalenceSurchargePercentage | Decimal | Read-only | Surcharge percentage |

### Totals

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalNet | Decimal | No | Amount after discounts, before taxes |
| totalGross | Decimal | No | Total amount before discounts and taxes |
| totalDiscount | Decimal | No | Sum of all discounts applied |
| totalCustomerDiscount | Decimal | No | Discount specific to the customer |
| totalLiquid | Decimal | No | Final amount due, including taxes |
| totalVat | Decimal | No | Total Value Added Tax (VAT) applied |
| totalVatBase | Decimal | No | Total taxable base before VAT |
| totalFeeSurcharge | Decimal | No | Total equivalence surcharge applied, based on VAT |
| totalVatFee | Decimal | No | VAT amount used as the base for the surcharge |
| totalLiquidNoWithholding | Decimal | No | Line total excluding withholding |
| totalWithholding | Decimal | No | Line withholding amount |
| withholdingPercentage | Decimal | No | Withholding percentage applied to the line |

### Discounts

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| firstDiscount | Decimal | No | First discount percentage |

### Specific to Sales Quote Document Lines

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| pendingQuantity | Decimal | Read-only | Pending Quantity |
| status | String (15) | No | Possible values: Pending, Completed, Partial, Declined |

### Info (Lines)

- **unitOfMeasurementId**: UUID specifying the ID of the unit of measurement associated with the product.
- **firstDiscount**: Various levels of discounts applicable to the document line. These are optional fields and are usually filled in based on promotional schemes or contractual obligations.
- **productId**: The unique identifier for the product. Automatically filled when selecting a product.
- **vatPercentage**: The percentage of Value Added Tax (VAT) applicable to this line. Important for accounting and for generating accurate invoices.
- **equivalenceSurchargePercentage**: The percentage of equivalence surcharge applied in Spain.
- **totalLiquidNoWithholding / totalWithholding / withholdingPercentage**: Unused (Spain only). The following fields are specific to the Spanish legislation (IRPF): totalLiquidNoWithholding is line total amount excluding withholding tax. totalWithholding is withholding amount (IRPF) calculated for the document line. withholdingPercentage is withholding percentage (IRPF) applied to the line and used to calculate the withholding amount.
- **order**: This field represents the position of a line within a document. It is read-only and is automatically calculated during creation or update. Its purpose is to reflect the order in which the user entered the lines, as shown in the Sage Active interface.

### Preserving Line Order During Creation or Update

Although the order field cannot be set manually, its value is inferred from the order of lines in the payload.

**On creation:**
The position of lines in the payload determines their order. The first line gets order = 1, the second order = 2, etc. Make sure to sort the lines in the desired order before sending the payload.

**On update:**
Lines are re-ordered from 1 to n based on the payload. Since the update follows a PATCH logic, you may choose between:
- Sending only the changes (modified lines, new lines, lines marked for deletion)
- Or sending all the lines (including unchanged ones) in the desired order

In both cases:
- Lines with requestedAction = DELETED are excluded from the new numbering since they will be removed.
- New lines are appended after the last remaining line and automatically numbered accordingly.

### Specific to Sales Quote Document Lines Info

- **pendingQuantity**: The quantity of the sales quote line that is still pending. This integer represents the amount of product yet to be fulfilled.
- **status**: Indicates the current status of the Sales Quote Line, reflecting its progression in the sales process. Possible statuses include:
  - Pending: The quote line is awaiting approval or further action.
  - Completed: The quote line has been fully approved and is finalized.
  - Partial: The quote line has been partially approved or is awaiting completion of certain conditions.
  - Declined: The quote line is declined and can no longer be modified.

## salesQuotes Addresses

Do not use the addresses[] array. Instead, use mainAddress for the primary address and deliveryAddress for the delivery address. Each of these objects contains only one address, representing a singular primary or delivery address, respectively.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| countryId | UUID | No | Country Id |
| country | Country | No | Fields of Country (DATALOADER) |
| countryIsoCodeAlpha2 | String (2) | No | Country Code |
| countryName | String | Read-only | Country Name |
| name | String (50) | No | Code |
| firstLine | String (35) | No | First Line |
| secondLine | String (35) | No | Second Line |
| city | String (35) | No | Town |
| zipCode | String (9) | No | Postal Code |
| province | String (25) | No | Province / Region / Federal State |

### Info (Addresses)

- **countryIsoCodeAlpha2**: ISO2 country code. This field can be used for creation and serves as a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.

---
