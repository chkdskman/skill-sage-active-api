# Sales Delivery Notes — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/salesdelivery

## HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | createSalesDeliveryNote | SalesDeliveryNoteCreateGLDtoInput |
| POST | Update | Mutation | updateSalesDeliveryNote | SalesDeliveryNoteUpdateGLDtoInput |
| POST | Delete | Mutation | deleteSalesDeliveryNote | SalesDeliveryNoteDeleteGLDtoInput |
| POST | Read | Query | salesDeliveryNotes filtered by id | |
| POST | List | Query | salesDeliveryNotes | |
| POST | List | Query | salesDeliveryNoteLines | |

## Quick Tips

- It is possible to query either salesDeliveryNote or directly from salesDeliveryNoteLines.
- Querying salesDeliveryNotes allows you to retrieve entire documents with all their associated line items. This is useful when you need the complete context of a document, including all details of the related lines.
- Querying salesDeliveryNoteLines directly is beneficial when you want to filter or aggregate specific line item data across multiple documents, such as analyzing product sales, quantities, or prices.
- More info: API resources overview / Filter vs. Direct Line Filtering

## Description

Both known as Sales delivery notes in the API context and in the product interface. This resource is the backbone for organizing and managing the shipping and handling details associated with sales orders.

With the flexibility to incorporate both standard and customized fields, Sales Delivery Notes offer granular visibility into the logistical aspects of the order-to-cash process. This is critical for ensuring timely and accurate deliveries to customers.

To see examples of mutations that include the adding of DeliveryNote: download the Postman collection: Quick start / 5. Test your first query in Postman.

To change the Status of a salesDeliveryNote, refer to the global sales documents page, which details the available options depending on the document type. See: API resources overview / Update Document Status.

### Example: Create a Sales Delivery Note

**GraphQL Mutation:**

```graphql
mutation ($values: SalesDeliveryNoteCreateGLDtoInput!) {
  createSalesDeliveryNote(input: $values) {
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
    "createSalesDeliveryNote": {
      "id": "e451aff7-074f-4e72-a6fb-4ea25e9d9c64",
      "operationalNumber": "0027"
    }
  }
}
```

## Request Header

| Key | Value |
|-----|-------|
| Authorization | Bearer {Current access Token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

## salesDeliveryNotes Fields

### Common to All Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system field, cannot be assigned) |
| creationDate | DateTime | System | Creation Date (system field, cannot be assigned) |
| modificationDate | DateTime | System | Modification Date (system field, cannot be assigned) |
| sourceType | SALES / PUBLIC_API | System | Indicates the origin of the salesDeliveryNote (SALES=Manual entry) |

### General Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| customer | Customer | — | Customer object (DATALOADER) |
| customerId | UUID | Yes (not modifiable after creation) | ID of the Customer entity. Not mandatory for Quote |
| documentDate | DateTime | — | Document Date (only date value is considered, time is ignored) |
| operationalNumber | String (50) | — | Document Number |
| operationalNumberPresetText | OperationalNumberPresetText | — | Operational number preset text (DATALOADER, coming soon) |
| operationalNumberPresetTextId | UUID | — | ID of the operational number preset text (coming soon) |
| status | String (15) | — | Pending or Closed |
| socialName | String (50) | Not modifiable after creation | Social Name |
| contactName | String (50) | — | Contact Name |
| contactPhone | String (15) | — | Contact Phone |
| contactEmail | String (50) | — | Contact Email |
| contactJobAreaId | UUID | — | Id of the Contact Job Area |
| countryAcronym | String (2) | — | Country Acronym |

### Delivery Note Lines

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| lines[] | Array | Yes | Document Lines |

### Totals and Discount

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalNet | Decimal | — | Amount after discounts, before taxes |
| totalGross | Decimal | — | Total amount before discounts and taxes |
| totalDiscount | Decimal | — | Sum of all discounts applied |
| totalCustomerDiscount | Decimal | — | Discount specific to the customer |
| totalLiquid | Decimal | — | Final amount due, including taxes |
| totalVat | Decimal | — | Total Value Added Tax (VAT) applied |
| totalVatBase | Decimal | — | Total taxable base before VAT |
| totalFeeSurcharge | Decimal | — | Total equivalence surcharge applied, based on VAT |
| totalVatFee | Decimal | — | VAT amount used as the base for the surcharge |
| discount | Decimal | — | Specific discount applied |
| totalLiquidNoWithholding | Decimal | — | Total amount excluding withholding |
| totalWithholding | Decimal | — | Total withholding amount |

### Notes

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| comments | String (1000) | — | Notes for the customer/recipient of the document |
| remarks | String (1000) | — | Internal notes for the document |
| externalReference | String (20) | — | External customer reference provided to facilitate communication and tracking |

### Customer Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| documentId | String (20) | — | Identification Number |
| documentLanguage | ENGLISH / FRENCH / GERMAN / SPANISH | — | Language used in sales Document PDF |
| documentType | DocumentType | — | Document type for identification purposes (DATALOADER) |
| documentTypeId | UUID | — | Identifier of the document type |
| vatNumber | String (20) | — | VAT Number |
| invoiceEmail | String (50) | — | Invoice Email (new feature) |
| hasEquivalenceSurcharge | Boolean | — | Subject to the equivalence surcharge regime |
| mainAddress | ObjectType | — | Main Address |
| deliveryAddress | ObjectType | — | Delivery Address |
| salesDiscountGroup | SalesDiscountGroup | — | Sales discount group (DATALOADER) |
| salesDiscountGroupId | UUID | — | Sales Discount ID |
| salesTariff | SalesTariff | — | Sales tariff (DATALOADER) |
| salesTariffId | UUID | — | Sales tariff ID |

### Specific to Sales Delivery Note Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| updateDocumentStatus | CLOSE | Mutation only | Allows changing the status of the delivery note |

## Field Details (Info)

- **customerId**: A unique identifier (UUID) for the Customer entity.
- **totalGross**: Represents the total amount before any discounts or taxes are applied. It includes the sum of all item prices at their full value.
- **totalDiscount**: The aggregated discount amount applied to the document, including all types of discounts that reduce the gross total.
- **totalNet**: The total amount after applying discounts but before adding taxes. It represents the taxable base amount.
- **discount**: Represents a specific discount amount applied within the document. This can be a percentage or a fixed amount reduction.
- **totalCustomerDiscount**: A discount specifically applied based on customer-specific conditions, such as loyalty discounts or promotional offers tailored to the customer.
- **totalLiquid**: The total payable amount, which includes taxes and any additional fees. This represents the final amount due.
- **totalVat**: The total Value Added Tax (VAT) calculated on the taxable base. This amount represents the sum of all VAT applied across items in the document.
- **totalVatBase**: The total taxable base amount before VAT is applied. This is the sum of all net amounts subject to VAT.
- **totalLiquidNoWithholding / totalWithholding** (Spain only - IRPF):
  - totalLiquidNoWithholding: Total amount excluding withholding tax. This represents the taxable base before applying IRPF withholding.
  - totalWithholding: Total withholding amount (IRPF) calculated at document level.
- **hasEquivalenceSurcharge / totalFeeSurcharge / totalVatFee** (unused for French and German markets):
  - Indicates whether a customer is subject to the equivalence surcharge regime.
  - The equivalence surcharge (totalFeeSurcharge), as applied in Spain, is a form of simplified VAT for retail traders who cannot recover VAT.
  - The rates of the equivalence surcharge vary according to the VAT rate applicable to the products, with surcharge rates of 5.2% for products with a general VAT of 21%, 1.4% for products with a reduced VAT of 10%, and other rates for different products or categories.
  - The equivalence surcharge is calculated on amount of general VAT (totalVatFee).
- **socialName**:
  - Mandatory for the French market
  - Not mandatory for the Spanish market
  - Not mandatory for the German market
- **externalReference**: Allows entering an external reference provided by the customer to facilitate communication and tracking.
- **vatNumber**: An EU VAT Number is a unique identifier for tax purposes within the European Union, used primarily for cross-border transactions. It starts with a two-letter country code followed by a series of numbers and sometimes letters, varying by country. This field is not applicable for customers that are not registered in the VIES system. For VIES customers, the format is described at FAQ N 11: https://ec.europa.eu/taxation_customs/vies/#/faq. Mandatory for VIES customers, not mandatory for Spanish or German markets.
- **invoiceEmail**: Email address used when sending the Invoice PDF through DocumentPdfEmail. This email is required when creating a Sales Invoice and canGeneratePaymentLink is set to true, as it is used for online payment communication. If not provided, the Invoice can still be created, but the online payment email workflow will not be available.
- **documentId**: Identification number, value formats may differ depending on legislationCode.
  - French: you can fill any SIREN of 9 characters or any SIRET of 14 characters. Not mandatory.
  - German: you may enter either a Steuer-IdNr with exactly 11 characters or another value ranging from 11 to 14 characters. Not mandatory.
  - Spanish: you need to fill a valid NIF/DNI/NIE number. Mandatory.
- **documentLanguage**: Applies to section titles and column headers in sales documents PDFs.
- **documentTypeId**: This functionality determines the corresponding document type based on the document number provided. This determination is vital for verifying the real existence of a customer, thereby ensuring their authenticity and compliance with regulations.

### Status Values

- **Pending**: The delivery note is in progress and can be modified.
- **Closed**: The delivery can no longer be modified; it has either been transferred to an invoice or it has been closed.

## salesDeliveryNoteLines Fields

If the query targets salesDeliveryNotes, then use `lines` to get the details of the lines. If the query targets salesDeliveryNoteLines, access the following fields directly, including the option to retrieve information on the parent salesDeliveryNote.

### Common to All Documents

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system field, cannot be assigned) |
| creationDate | DateTime | System | Date of creation (system field, cannot be assigned) |
| modificationDate | DateTime | System | Last modification date (system field, cannot be assigned) |

### Parent Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| salesDeliveryNote | SalesDeliveryNote | — | Fields of the parent salesDeliveryNotes (DATALOADER) |
| salesDeliveryNoteId | UUID | — | salesDeliveryNote ID |

### Product Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| product | Product | — | Product object (DATALOADER) |
| productId | UUID | — | Product ID |
| productCode | String (15) | — | Product code |
| productName | String (2500) | — | Product name |
| order | Int | Read-only | Classification of lines from 1 to n |
| unitOfMeasurement | UnitOfMeasurement | — | Unit of measurement (DATALOADER) |
| unitOfMeasurementId | UUID | Default calculated | Unit of Measurement ID |

### Tax and Price Information

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalQuantity | Decimal | — | Quantity product |
| unitPrice | Decimal | — | Unit price |
| vatPercentage | Decimal | Read-only | VAT percentage |
| equivalenceSurchargePercentage | Decimal | Read-only | Surcharge percentage |

### Totals

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| totalNet | Decimal | — | Amount after discounts, before taxes |
| totalGross | Decimal | — | Total amount before discounts and taxes |
| totalDiscount | Decimal | — | Sum of all discounts applied |
| totalCustomerDiscount | Decimal | — | Discount specific to the customer |
| totalLiquid | Decimal | — | Final amount due, including taxes |
| totalVat | Decimal | — | Total Value Added Tax (VAT) applied |
| totalVatBase | Decimal | — | Total taxable base before VAT |
| totalFeeSurcharge | Decimal | — | Total equivalence surcharge applied, based on VAT |
| totalVatFee | Decimal | — | VAT amount used as the base for the surcharge |
| totalLiquidNoWithholding | Decimal | — | Line total excluding withholding |
| totalWithholding | Decimal | — | Line withholding amount |
| withholdingPercentage | Decimal | — | Withholding percentage applied to the line |

### Discounts

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| firstDiscount | Decimal | — | First discount percentage |

### Line Field Details (Info)

- **unitOfMeasurementId**: UUID specifying the ID of the unit of measurement associated with the product.
- **firstDiscount**: Various levels of discounts applicable to the document line. These are optional fields and are usually filled in based on promotional schemes or contractual obligations.
- **productId**: The unique identifier for the product. Automatically filled when selecting a product.
- **vatPercentage**: The percentage of Value Added Tax (VAT) applicable to this line. Important for accounting and for generating accurate invoices.
- **equivalenceSurchargePercentage**: The percentage of equivalence surcharge applied in Spain.
- **totalLiquidNoWithholding / totalWithholding / withholdingPercentage** (Spain only - IRPF):
  - totalLiquidNoWithholding: Line total amount excluding withholding tax.
  - totalWithholding: Withholding amount (IRPF) calculated for the document line.
  - withholdingPercentage: Withholding percentage (IRPF) applied to the line and used to calculate the withholding amount.
- **order**: This field represents the position of a line within a document. It is read-only and is automatically calculated during creation or update. Its purpose is to reflect the order in which the user entered the lines, as shown in the Sage Active interface.

### Preserving Line Order During Creation or Update

Although the order field cannot be set manually, its value is inferred from the order of lines in the payload.

**On creation:** The position of lines in the payload determines their order. The first line gets order = 1, the second order = 2, etc. Make sure to sort the lines in the desired order before sending the payload.

**On update:** Lines are re-ordered from 1 to n based on the payload. Since the update follows a PATCH logic, you may choose between:
- Sending only the changes (modified lines, new lines, lines marked for deletion)
- Or sending all the lines (including unchanged ones) in the desired order

In both cases:
- Lines with requestedAction = DELETED are excluded from the new numbering since they will be removed. (More info: updating records)
- New lines are appended after the last remaining line and automatically numbered accordingly.

## salesDeliveryNotes Addresses

Do not use the `addresses[]` array. Instead, use `mainAddress` for the primary address and `deliveryAddress` for the delivery address. Each of these objects contains only one address, representing a singular primary or delivery address, respectively.

### Address Fields (Common to All Documents)

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| countryId | UUID | — | Country Id |
| country | Country | — | Country object (DATALOADER) |
| countryIsoCodeAlpha2 | String (2) | — | Country Code (ISO2) |
| countryName | String | Read-only | Country Name |
| name | String (50) | — | Code |
| firstLine | String (35) | — | First Line |
| secondLine | String (35) | — | Second Line |
| city | String (35) | — | Town |
| zipCode | String (9) | — | Postal Code |
| province | String (25) | — | Province / Region / Federal State |

### Address Field Details (Info)

- **countryIsoCodeAlpha2**: ISO2 country code. This field can be used for creation and serves as a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.

---
