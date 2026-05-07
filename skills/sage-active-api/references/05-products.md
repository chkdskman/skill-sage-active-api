# Products, Tariffs & Discounts — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/products
> - https://developer.sage.com/sageactive/resources/productsmanagement
> - https://developer.sage.com/sageactive/resources/productprices
> - https://developer.sage.com/sageactive/resources/tariffs
> - https://developer.sage.com/sageactive/resources/discounts

## Overview

This section outlines the foundational role of Products in the scope of sales transactions. Products serve as the foundational elements for creating and managing sales transactions. The Products entity is pivotal in creating and managing Sales Quotes, Sales Orders, Sales Delivery Notes, and Sales Invoices.

### Product

Products, both identified as Product in the API context and in the product interface, serve as the foundational elements for creating and managing sales transactions.

In the scope of sales documents, such as quotes, orders, delivery notes, and invoices, the Products entity plays a pivotal role. Each product entry can include a comprehensive set of details, from basic identifiers like the product code and name, to more complex attributes like tax codes, discount rates, and stock levels.

This enables seamless generation and manipulation of various sales documents, ensuring both accuracy and consistency across transactions.

Additionally, the Products entity can be associated with various pricing models, such as standard rates, special discounts, promotional offers, and tiered pricing strategies. These associations help in automating the calculation of final costs in sales documents, making the process more efficient and less prone to errors.

### Tariff

Sales Tariffs, known as SalesTariff in the API context and Prices in the product interface, form the structural framework essential for crafting and administering pricing strategies within sales endeavors.

Each tariff (salesTariffs) is built upon a calculation type (new enforced price, % increment, value increment), under which specified products are categorized. For each product added to the tariff (salesTariffLines), a value indicator is determined based on the calculation type. Moreover, it's feasible to modulate the value indicator according to ranges of minimum to maximum quantities (salesTariffLineStretch).

### Discount

Sales Discounts, known as SalesDiscount in the API context and Discounts in the product interface. This resource is crucial for managing discount strategies within your sales workflow. It allows for the configuration of various types of discounts, such as volume-based discounts, seasonal discounts, or special promotional discounts, providing flexibility in sales promotions and revenue management.

### Price Service

The ProductPrice service is designed to provide detailed pricing and discount information for a product. This pricing and discounting information is calculated based on a variety of rules and parameters, ensuring the most accurate and tailored result for your specific needs. The service returns both the calculated discount and price for the product.

The service takes into account:

- **Base Price and Discount:** These are the default price and discount set directly on the product.
- **Tariff Management Rules:** Includes the rules defined in the tariff management system which may apply additional costs or discounts.
- **Discount Management Rules:** Includes rules set in the discount management system which may apply extra discounts.

### Workflow

1. **Product Creation:** Initial setup of a product with its attributes.
2. **Pricing Strategy:** Determining various pricing models that can be associated with the product.
3. **Sales Document Inclusion:** Products are included in various sales documents like quotes, orders, delivery notes, and invoices.

---

## Products

### HTTP Operations

| Operation | Type | Object | DTO |
|-----------|------|--------|-----|
| POST Create | Mutation | createProduct | ProductCreateGLDtoInput |
| POST Update | Mutation | updateProduct | ProductUpdateGLDtoInput |
| POST Delete | Mutation | deleteProduct | ProductDeleteGLDtoInput |
| POST Read | Query | products filtered by id | — |
| POST List | Query | products | — |

### Description

Products, both identified as Product in the API context and in the product interface, serve as the foundational elements for creating and managing sales transactions.

In the scope of sales documents, such as quotes, orders, delivery notes, and invoices, the Products entity plays a pivotal role. Each product entry can include a comprehensive set of details, from basic identifiers like the product code and name, to more complex attributes like tax codes, discount rates, and stock levels. This enables seamless generation and manipulation of various sales documents, ensuring both accuracy and consistency across transactions.

Additionally, the Products entity can be associated with various pricing models, such as standard rates, special discounts, promotional offers, and tiered pricing strategies. These associations help in automating the calculation of final costs in sales documents, making the process more efficient and less prone to errors.

### Create Product Example

**GraphQL Mutation:**

```graphql
mutation ($values: ProductCreateGLDtoInput!) {
  createProduct(input: $values) {
    id
  }
}
```

**GraphQL Variables:**

```json
{
  "values": {
    "code": "SILVRING",
    "name": "Silver Ring",
    "lineDescription": "This ring showcases a timeless design that complements any outfit.",
    "comments": "This ring has been a bestseller in previous collections.",
    "salesUnitPrice": 400,
    "taxGroupId": "{taxGroupId}"
  }
}
```

**Example Response:**

```json
{
  "data": {
    "createProduct": {
      "id": "e451aff7-074f-4e72-a6fb-4ea25e9d9c64"
    }
  }
}
```

### Request Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### Products Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | ID (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| category | Enum: NOT_SPECIFIED, CONCEPT, FEE, PRODUCT, PRODUCT_AND_SERVICE, SERVICE | Default | Product Category. Default value is PRODUCT. |
| code | String (15) | Yes | Product Code |
| name | String (50) | Yes | Product Name |
| registrationDate | DateTime | Read-only | Timestamp for tracking and auditing (date only) |
| obsolete | Boolean | Default | Whether the product is obsolete. Default is false. Can only be set when modifying, not during creation. |
| obsoleteDate | DateTime | Read-only | Date when obsolete was enabled (date only) |
| unitOfMeasurement | UnitOfMeasurement | — | Fields of Unit of Measurement (dataloader) |
| unitOfMeasurementId | UUID | Default | Unit of Measurement ID. Links the product to a predefined unit of measurement. |
| lineDescription | String (2500) | No | Product description visible to the customer. |
| comments | String (250) | No | Internal information, e.g. a sales argument. |
| taxes[] | Tax | — | List of taxes from the product's tax group (dataloader). |
| taxGroupId | UUID | Default | Tax Group ID. Default is the one defined in Configuration / Company / Sales and Operations / Tax group. |
| salesAccountingAccount | AccountingAccount | — | Fields of Sales Accounting Account (dataloader) |
| salesAccountingAccountId | UUID | Default | Sales Accounting Account ID. Default is the one defined in Configuration / Company / Accounting / Sales account. |
| salesUnitPrice | Decimal | Default | Sales Unit Price |
| salesVatPercentage | Decimal | Read-only | Sales VAT Percentage. Retrieved from taxes via taxGroupId / percentage. |
| firstSalesDiscount | Decimal | No | First Sales Discount |
| useWithholding | Boolean | No | Indicates if withholding applies (Spain only - IRPF). If true, withholding can be calculated on sales documents. |
| salesOrderBlockingEnabled | Boolean | Default | Is Sales Order Blocking Enabled |
| salesOrderBlockingDate | DateTime | Read-only | Date when Sales Order Blocking was enabled (date only) |
| salesDeliveryNoteBlockingEnabled | Boolean | Default | Is Sales Delivery Note Blocking Enabled |
| salesDeliveryNoteBlockingDate | DateTime | Read-only | Date when Sales Delivery Note Blocking was enabled (date only) |
| salesInvoiceBlockingEnabled | Boolean | Default | Is Sales Invoice Blocking Enabled |
| salesInvoiceBlockingDate | DateTime | Read-only | Date when Sales Invoice Blocking was enabled (date only) |

---

## Product Prices

### HTTP Operations

| Operation | Type | Object | DTO |
|-----------|------|--------|-----|
| POST Read | Action (Query) | productPriceById | ProductPriceGLDtoInput |

### Description

The ProductPrice service is designed to provide detailed pricing and discount information for a product. This pricing and discounting information is calculated based on a variety of rules and parameters, ensuring the most accurate and tailored result for your specific needs. The service returns both the calculated discount and price for the product.

#### Pricing and Discount Calculations

The service takes into account:

- **Base Price and Discount:** These are the default price and discount set directly on the product.
- **Tariff Management Rules:** Includes the rules defined in the tariff management system which may apply additional costs or discounts.
- **Discount Management Rules:** Includes rules set in the discount management system which may apply extra discounts.

#### Required Parameters

To utilize this service, you need to provide the following parameters:

- **Product ID:** The unique identifier of the product for which the price and discounts are being calculated.
- **Customer ID:** The unique identifier of the customer for whom the price is being calculated.
- **Customer Tariff ID:** Optionally, the ID that represents the pricing tariff applicable to the customer. If left empty, the system will automatically use the tariff specified in the customer's 'Sales terms and conditions' tab.
- **Customer Discount ID:** Optionally, the ID for the discount group applicable to the customer. If left empty, the system will automatically use the discount specified in the customer's 'Sales terms and conditions' tab.
- **Document Date:** The date of the document for time-sensitive pricing and discount rules.
- **Quantity:** The number of items for which the price is being calculated.
- **Document Type ID:** Defines the type of identification document associated with the lead's country, used only if the quote is not linked to a customer but is a quote for a lead without Customer Id.

Providing the Customer Tariff ID and/or Customer Discount ID allows for the manual overriding of the automatic rates set in the customer's 'Sales terms and conditions' tab, thus enabling different rates to be applied within the context of the document.

#### Response

The service returns a comprehensive set of data fields that include the final calculated price, applicable discounts, and tax details. This takes into consideration all the rules and parameters you have supplied.

### Request Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### ProductPrice Query Example (for a customer)

**GraphQL Query:**

```graphql
query($id: ID!, $productPrice: ProductPriceGLDtoInput!) {
  productPriceById(id: $id, productPrice: $productPrice) {
    equivalenceSurchargePercentage
    firstDiscount {
      code
      name
      source
      value
    }
    price {
      code
      name
      source
      value
    }
    taxGroupId
    taxId
    taxPercentage
    taxTreatmentId
  }
}
```

**GraphQL Variables:**

```json
{
  "id": "{productId}",
  "productPrice": {
    "customerId": "{customerId}",
    "salesTariffId": "{salesTariffId}",
    "salesDiscountGroupId": "{salesDiscountGroupId}",
    "documentDate": "2023-12-05",
    "quantity": 5
  }
}
```

**Example Response:**

```json
{
  "data": {
    "productPriceById": {
      "price": {
        "code": "UNKNOWN",
        "name": "UNKNOWN",
        "source": "UNKNOWN",
        "value": 100
      },
      "firstDiscount": {
        "code": "UNKNOWN",
        "name": "UNKNOWN",
        "source": "UNKNOWN",
        "value": 0
      },
      "taxGroupId": "285afa97-bdf1-45c2-b258-924fd7410082",
      "taxId": "808f93b0-b7a2-472d-a0e6-686a4b892778",
      "equivalenceSurchargePercentage": 0,
      "taxPercentage": 20,
      "taxTreatmentId": "7a58c924-c7b1-4027-9685-e7935e107a85"
    }
  }
}
```

### productPriceById Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | Yes | Product ID |
| customerId | UUID | No | Customer ID. The unique identifier for the customer for whom the product price and discount are calculated. |
| salesTariffId | UUID | No | Sales Tariff ID. If left empty, the system will automatically use the tariff specified in the customer's 'Sales terms and conditions' tab. |
| salesDiscountGroupId | UUID | No | Sales Discount Group ID. If left empty, the system will automatically use the discount specified in the customer's 'Sales terms and conditions' tab. |
| documentDate | DateTime | No | Document Date (date only) |
| quantity | Decimal | No | Quantity |
| documentTypeId | UUID | No | Document Type Id. Used only if the quote is not linked to a customer but is a quote for a lead. Do not provide if customerId is specified. Required if the lead's country is part of the VIES system (code 02) or is outside VIES and different from current legislation (code 06). |

### productPriceById Response Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| firstDiscount | ComputedDiscountGLDto | — | Discount Details |
| price | ComputedUnitPriceGLDto | — | Price Details |
| taxGroupId | UUID | — | Tax Group ID. A unique identifier for the tax group associated with the product price. |
| taxId | UUID | — | Tax ID |
| taxTreatmentId | UUID | — | Tax Treatment ID. Identifies the treatment of tax for the product (e.g. inclusive or exclusive). |
| taxPercentage | Decimal | — | Tax Percentage. Represents the percentage of tax applied to the product price. |
| equivalenceSurchargePercentage | Decimal | — | Percentage of the equivalence surcharge. Always 0 for FR and DE. For ES, returns the surcharge if the customer has hasEquivalenceSurcharge set to true. Rates: 5.2% for 21% VAT, 1.4% for 10% VAT. |

### productPrice/firstDiscount Response Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| code | String (2) | — | Discount Code. A short code uniquely identifying the discount. |
| name | String (50) | — | Discount Name |
| source | String (10) | — | Discount Source. Describes the origin of the discount (e.g. promotional campaign or loyalty program). |
| value | Decimal | — | Discount Value |

### productPrice/price Response Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| code | String (2) | — | Price Code. A short code uniquely identifying the price. |
| name | String (50) | — | Price Name |
| source | String (10) | — | Price Source. Describes the origin of the price. |
| value | Decimal | — | Price Value |
| indicator | String (5) | — | Special price indicator: "PRICE" if a special price exists, null when there is no special price. |

---

## Sales Tariffs

### HTTP Operations

| Operation | Type | Object |
|-----------|------|--------|
| POST Read | Query | salesTariffById |
| POST List | Query | salesTariffs |

### Description

Sales Tariffs, known as SalesTariff in the API context and Prices in the product interface, form the structural framework essential for crafting and administering pricing strategies within sales endeavors.

Each tariff (salesTariffs) is built upon a calculation type (new enforced price, % increment, value increment), under which specified products are categorized. For each product added to the tariff (salesTariffLines), a value indicator is determined based on the calculation type. Moreover, it's feasible to modulate the value indicator according to ranges of minimum to maximum quantities (salesTariffLineStretch).

### Request Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### salesTariffs Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| code | String (10) | Yes | Sales tariff code |
| name | String (20) | Yes | Name of tariff |
| type | Enum: NONE, INCREMENT, MARGIN, PRICE | Yes | Type of tariff. PRICE: enforces a price replacing the base price. MARGIN: applies a percentage increase/decrease. INCREMENT: adds/subtracts an amount to the base price. |
| startDate | DateTime | No | Start date for tariff (date only). Useful for seasonal or temporary tariffs. |
| endDate | DateTime | No | End date for tariff (date only). Useful for seasonal or temporary tariffs. |
| enabled | Boolean | No | Whether tariff is enabled. A boolean flag indicating whether the tariff is currently active. |
| lines[] | Array | Yes | Collection of tariff lines |

### salesTariffs/salesTariffLines Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| product | Product | — | Fields of Product (dataloader) |
| productId | UUID | No | Product ID |
| startDate | DateTime | No | Start Date (date only). The start date of the validity period for this sales tariff line. |
| endDate | DateTime | No | End Date (date only). The end date of the validity period for this sales tariff line. |
| enabled | Boolean | Yes | Enabled Status |
| indicatorValue | Decimal | No | Indicator Value. A decimal value that affects the item's price, depending on the type field (PRICE, MARGIN, INCREMENT). |
| stretchs[] | Array | No | List of Tariff Line Stretches. An array containing the different stretches or phases within the sales tariff line. |

### salesTariffs/salesTariffLines/salesTariffLineStretch Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| fromUnits | Decimal | No | From Units. Specifies the starting unit range for which this sales tariff line is applicable. |
| toUnits | Decimal | No | To Units. Specifies the ending unit range. May be left empty to indicate no upper limit. |
| indicatorValue | Decimal | No | Indicator Value |

---

## Sales Discounts

### HTTP Operations

| Operation | Type | Object |
|-----------|------|--------|
| POST Read | Query | salesDiscountById |
| POST List | Query | salesDiscounts |

### Description

Sales Discounts, known as SalesDiscount in the API context and Discounts in the product interface. This resource is crucial for managing discount strategies within your sales workflow. It allows for the configuration of various types of discounts, such as volume-based discounts, seasonal discounts, or special promotional discounts, providing flexibility in sales promotions and revenue management.

### Request Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | Current tenant id (deprecated) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### salesDiscounts Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| code | String (10) | Yes | Discount Group Code |
| name | String (20) | Yes | Discount Group Name |
| startDate | DateTime | No | Start Date (date only). Specifies the date this discount group becomes active. |
| endDate | DateTime | No | End Date (date only). Specifies the date this discount group is supposed to end. |
| enabled | Boolean | No | Is Enabled. A boolean flag indicating whether this sales discount group is currently enabled or disabled. |
| lines[] | Array | Yes | List of Sales Discount Group Lines. An array of SalesDiscountGroupLineDto objects. |

### salesDiscounts/salesDiscountLines Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| product | Product | — | Fields of Product (dataloader) |
| productId | UUID | No | Product ID. A unique identifier for the product associated with the sales discount group line. |
| startDate | DateTime | No | Start Date (date only) |
| endDate | DateTime | No | End Date (date only) |
| enabled | Boolean | No | Is Enabled |
| firstDiscount | Decimal | No | First Discount Rate. Specifies the first discount rate applied to products in this line. |
| stretchs[] | Array | No | List of Sales Discount Group Line Stretches. An array of SalesDiscountGroupLineStretchDto objects. |

### salesDiscounts/salesDiscountLines/salesDiscountLineStretch Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id (system-assigned) |
| creationDate | DateTime | System | Creation Date (system-assigned) |
| modificationDate | DateTime | System | Modification Date (system-assigned) |
| fromUnits | Decimal | No | From Units. Specifies the starting unit count for the discount stretch. |
| toUnits | Decimal | No | To Units. Specifies the ending unit count. Can be left null to indicate no upper limit. |
| firstDiscount | Decimal | No | First Discount Rate. Specifies the first discount rate applied to the stretch. |
