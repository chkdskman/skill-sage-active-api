# Sales Orders — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/salesorders

## HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | createSalesOrder | SalesOrderCreateGLDtoInput |
| POST | Update | Mutation | updateSalesOrder | SalesOrderUpdateGLDtoInput |
| POST | Delete | Mutation | deleteSalesOrder | SalesOrderDeleteGLDtoInput |
| POST | Read | Query | salesOrders filtered by id | |
| POST | List | Query | salesOrders | |
| POST | List | Query | salesOrderLines | |

### Quick Tips

- It is possible to query either salesOrder or directly from salesOrderLines.
- Querying salesOrders allows you to retrieve entire documents with all their associated line items. This is useful when you need the complete context of a document, including all details of the related lines.
- Querying salesOrderLines directly is beneficial when you want to filter or aggregate specific line item data across multiple documents, such as analyzing product sales, quantities, or prices.

## Description

Both known as Sales orders in the API context and in the product interface.

This resource serves as the comprehensive record for managing customer orders within the order-to-cash process. Utilizing the standardized and custom data fields, it captures detailed information such as customer codes, discounts, and product lines, which are essential for fulfilling customer orders.

To see examples of mutations that include the adding of Order: download the Postman collection: Quick start / 5. Test your first query in Postman.

To change the Status of a salesOrder, refer to the global sales documents page, which details the available options depending on the document type.

## Example: Create a Sales Order

### GraphQL Mutation

```graphql
mutation ($values: SalesOrderCreateGLDtoInput!) {
  createSalesOrder(input: $values) {
    id
    operationalNumber
  }
}
```

### GraphQL Variables

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

### Example Response

```json
{
  "data": {
    "createSalesOrder": {
      "id": "e451aff7-074f-4e72-a6fb-4ea25e9d9c64",
      "operationalNumber": "0027"
    }
  }
}
```

## Header

| Key | Value |
|-----|-------|
| Authorization | Bearer Current access Token |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

## salesOrders Fields

### Common to all documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id — system field that cannot be assigned |
| creationDate | DateTime | System | Creation Date — system field that cannot be assigned |
| modificationDate | DateTime | System | Modification Date — system field that cannot be assigned |
| sourceType | SALES / PUBLIC_API | System | Indicates the origin of the salesOrder (SALES=Manual entry) |

### General Info

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| customer | Customer | | DATALOADER — Fields of Customer |
| customerId | UUID | Yes (not modifiable after creation) | ID of the Customer entity. Not mandatory for Quote |
| documentDate | DateTime | | Document Date (only date value considered, time ignored) |
| operationalNumber | String(50) | | Document Number |
| operationalNumberPresetText | OperationalNumberPresetText | | DATALOADER — Fields of OperationalNumberPresetText. 🚧 COMING SOON (announced ≤ 2026-03) |
| operationalNumberPresetTextId | UUID | | ID of the operational number preset text. 🚧 COMING SOON (announced ≤ 2026-03) |
| status | String(15) | | Pending / Closed |
| socialName | String(50) | Not modifiable after creation | Social Name |
| contactName | String(50) | | Contact Name |
| contactPhone | String(15) | | Contact Phone |
| contactEmail | String(50) | | Contact Email |
| contactJobAreaId | UUID | | Id of the Contact Job Area |
| countryAcronym | String(2) | | Country Acronym |

### Order Lines

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| lines[] | Array | Yes | Document Lines |

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
| comments | String(1000) | | Notes for the customer/recipient of the document |
| remarks | String(1000) | | Internal notes for the document |
| externalReference | String(20) | | External customer reference provided to facilitate communication and tracking |

### Customer Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| documentId | String(20) | | Identification Number |
| documentLanguage | String(20) | | ENGLISH / FRENCH / GERMAN / SPANISH — Language used in sales Document PDF |
| documentType | DocumentType | | DATALOADER — Fields of DocumentType used for identification purposes |
| documentTypeId | UUID | | Identifier of the document type |
| vatNumber | String(20) | | VAT Number |
| invoiceEmail | String(50) | | Invoice Email (new feature) |
| hasEquivalenceSurcharge | Boolean | | Subject to the equivalence surcharge regime |
| mainAddress | ObjectType | | Main Address |
| deliveryAddress | ObjectType | | Delivery Address |
| salesDiscountGroup | SalesDiscountGroup | | DATALOADER — Fields of SalesDiscount |
| salesDiscountGroupId | UUID | | Sales Discount ID |
| salesTariff | SalesTariff | | DATALOADER — Fields of SalesTariff |
| salesTariffId | UUID | | Sales tariff ID |

### Specific to Sales Order Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| updateDocumentStatus | CLOSE | Mutation only | Allows changing the status of the order |

### Info — Field Details

- **customerId**: A unique identifier (UUID) for the Customer entity.
- **totalGross**: Represents the total amount before any discounts or taxes are applied. It includes the sum of all item prices at their full value.
- **totalDiscount**: The aggregated discount amount applied to the document, including all types of discounts that reduce the gross total.
- **totalNet**: The total amount after applying discounts but before adding taxes. It represents the taxable base amount.
- **discount**: Represents a specific discount amount applied within the document. This can be a percentage or a fixed amount reduction.
- **totalCustomerDiscount**: A discount specifically applied based on customer-specific conditions, such as loyalty discounts or promotional offers tailored to the customer.
- **totalLiquid**: The total payable amount, which includes taxes and any additional fees. This represents the final amount due.
- **totalVat**: The total Value Added Tax (VAT) calculated on the taxable base. This amount represents the sum of all VAT applied across items in the document.
- **totalVatBase**: The total taxable base amount before VAT is applied. This is the sum of all net amounts subject to VAT.
- **totalLiquidNoWithholding / totalWithholding**: Unused (Spain only). The following fields are specific to the Spanish legislation (IRPF):
  - totalLiquidNoWithholding: Total amount excluding withholding tax. This represents the taxable base before applying IRPF withholding.
  - totalWithholding: Total withholding amount (IRPF) calculated at document level.
- **hasEquivalenceSurcharge / totalFeeSurcharge / totalVatFee**: Unused for the French market. Indicates whether a customer is subject to the equivalence surcharge regime. The equivalence surcharge (totalFeeSurcharge), as applied in Spain, is a form of simplified VAT for retail traders who cannot recover VAT. The rates vary according to the VAT rate applicable to the products (5.2% for products with general VAT of 21%, 1.4% for products with reduced VAT of 10%). The equivalence surcharge is calculated on amount of general VAT (totalVatFee). Unused for the German market.
- **socialName**: Mandatory for the French market. Not mandatory for the Spanish market. Not mandatory for the German market.
- **externalReference**: Allows entering an external reference provided by the customer to facilitate communication and tracking.
- **vatNumber**: An EU VAT Number is a unique identifier for tax purposes within the European Union, used primarily for cross-border transactions. It starts with a two-letter country code followed by a series of numbers and sometimes letters, varying by country. This field is not applicable for customers that are not registered in the VIES system. For VIES customers, the format is described at FAQ N11 https://ec.europa.eu/taxation_customs/vies/#/faq. Mandatory for VIES customers. Not mandatory for Spanish market. Not mandatory for German market.
- **invoiceEmail**: Email address used when sending the Invoice PDF through DocumentPdfEmail. This email is required when creating a Sales Invoice and canGeneratePaymentLink is set to true, as it is used for online payment communication. If not provided, the Invoice can still be created, but the online payment email workflow will not be available.
- **documentId**: Identification number, value formats may differ depending on legislationCode.
  - French: Any SIREN of 9 characters or any SIRET of 14 characters. Not mandatory.
  - German: Either a Steuer-IdNr with exactly 11 characters or another value ranging from 11 to 14 characters. Not mandatory.
  - Spanish: A valid NIF/DNI/NIE number. Mandatory.
- **documentLanguage**: Applies to section titles and column headers in sales documents PDFs.
- **documentTypeId**: This functionality determines the corresponding document type based on the document number provided. This determination is vital for verifying the real existence of a customer, thereby ensuring their authenticity and compliance with regulations.
- **Status**:
  - Pending: The order is in progress and can be modified.
  - Closed: The order can no longer be modified; it has either been transferred to a delivery note or it has been closed.

## salesOrders/lines (or directly from salesOrderLines)

If the query targets salesOrders, then use lines to get the details of the lines. If the query targets salesOrderLines, access the following fields directly, including the option to retrieve information on the parent salesOrder.

### Common to all documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id — system field that cannot be assigned |
| creationDate | DateTime | System | Date of creation — system field that cannot be assigned |
| modificationDate | DateTime | System | Last modification date — system field that cannot be assigned |

### salesOrders Parent Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| salesOrder | SalesOrder | | DATALOADER — Fields of the parent salesOrders |
| salesOrderId | UUID | | salesOrder ID |

### Product Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| product | Product | | DATALOADER — Fields of Product |
| productId | UUID | | Product ID |
| productCode | String(15) | | Product code |
| productName | String(2500) | | Product name |
| order | Int | Read-only | Classification of lines from 1 to n |
| unitOfMeasurement | UnitOfMeasurement | | DATALOADER — Fields of Unit of Measurement |
| unitOfMeasurementId | UUID | | Unit of Measurement ID (default value auto-calculated on creation) |

### Tax and Price Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalQuantity | Decimal | | Quantity product |
| unitPrice | Decimal | | Unit price |
| vatPercentage | Decimal | Read-only | VAT percentage |
| equivalenceSurchargePercentage | Decimal | Read-only | Surcharge percentage |

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

### Specific to Sales Order Document Lines

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| salesQuoteLine | SalesQuoteLine | | DATALOADER — Fields of SalesQuoteLine |
| salesQuoteLineId | UUID | | Sales Quote Line ID |
| pendingQuantity | Decimal | Read-only | Pending Quantity |
| status | String(15) | | Pending / Completed / Partial |

### Info — Line Field Details

- **unitOfMeasurementId**: UUID specifying the ID of the unit of measurement associated with the product.
- **firstDiscount**: Various levels of discounts applicable to the document line. These are optional fields and are usually filled in based on promotional schemes or contractual obligations.
- **productId**: The unique identifier for the product. Automatically filled when selecting a product.
- **vatPercentage**: The percentage of Value Added Tax (VAT) applicable to this line. Important for accounting and for generating accurate invoices.
- **equivalenceSurchargePercentage**: The percentage of equivalence surcharge applied in Spain.
- **totalLiquidNoWithholding / totalWithholding / withholdingPercentage**: Unused (Spain only). The following fields are specific to the Spanish legislation (IRPF):
  - totalLiquidNoWithholding: Line total amount excluding withholding tax.
  - totalWithholding: Withholding amount (IRPF) calculated for the document line.
  - withholdingPercentage: Withholding percentage (IRPF) applied to the line and used to calculate the withholding amount.
- **order**: This field represents the position of a line within a document. It is read-only and is automatically calculated during creation or update. Its purpose is to reflect the order in which the user entered the lines, as shown in the Sage Active interface.

#### Preserving Line Order During Creation or Update

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

#### Specific to Sales Order Document Lines

- **pendingQuantity**: The quantity of the sales order line that is still pending. This integer represents the amount of product yet to be fulfilled.
- **salesQuoteLineId**: ID of the sales quote line that was used to define the order when generating an order from a quote.
- **status**: The current status of the Sales Order Line, indicating the processing stage of this specific line item within the order. Possible values are:
  - Pending: The line item is acknowledged but not yet in process.
  - Completed: The line item has been fully processed and shipped.
  - Partial: The line item is partially fulfilled with more items yet to be processed.

## salesOrders/addresses

### Common to all documents

Do not use the addresses[] array. Instead, use mainAddress for the primary address and deliveryAddress for the delivery address. Each of these objects contains only one address, representing a singular primary or delivery address, respectively.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| countryId | UUID | | Country Id |
| country | Country | | DATALOADER — Fields of Country |
| countryIsoCodeAlpha2 | String(2) | | Country Code |
| countryName | String | Read-only | Country Name |
| name | String(50) | | Code |
| firstLine | String(35) | | First Line |
| secondLine | String(35) | | Second Line |
| city | String(35) | | Town |
| zipCode | String(9) | | Postal Code |
| province | String(25) | | Province / Region / Federal State |

### Info — Address Field Details

- **countryIsoCodeAlpha2**: ISO2 country code. This field can be used for creation and serves as a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.

---
