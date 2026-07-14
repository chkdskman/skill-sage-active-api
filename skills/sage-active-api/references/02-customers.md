# Customers — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/customers

## HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | createCustomer | CustomerCreateGLDtoInput |
| POST | Update | Mutation | updateCustomer | CustomerUpdateGLDtoInput |
| POST | Delete | Mutation | deleteCustomer | CustomerDeleteGLDtoInput |
| POST | Read | Query | customers filtered by id | — |
| POST | List | Query | customers | — |

## Description

Customer management.

Allows the identification of a third-party Customer within an organization. It offers the option to add contacts within the Customer and their details, as well as define the Customer's default payment conditions.

Every Customer must have a code, which, depending on the organization's settings, can be manually entered or automatically generated. In the DE (German) legislation, automatic numbering is mandatory.

## How to Create a Customer

Creating a Customer record involves multiple variables, including the legislation (FR, ES, or DE) and the country of the Customer.

### Legislation-Specific Fields

Certain mandatory fields will depend on the legislation you are operating under (FR, ES, or DE).

### Country of the Customer (countryAcronym)

Understanding the country of the Customer is crucial as it introduces additional variables that can affect the mandatory and optional fields needed for Customer creation.

- **Same as Operating Legislation**: If the Customer's country is the same as your operating legislation.
- **Member of VIES**: If the Customer's country is different from your operating legislation and is a member of the VAT Information Exchange System (VIES).
- **Different from Legislation and VIES**: If the Customer's country doesn't fall under your operating legislation and is not a member of VIES.

### Common Rules

- `countryIsoCodeAlpha2` is a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.
- `code` is optional if the automatic numbering option (Sequence number) is enabled for the Customer resource. However, it can be set to enforce a specific value instead of requesting the assignment of a new sequential number.
- `documentType` allows you to define the type of identification document for the Customer. It is automatically set by default based on the legislation and country, and, with few exceptions, does not need to be filled in during creation.

## Examples of Creation

Below, for each legislation (FR, ES, or DE), the specific rules and differences depending on the Customer's country. These examples assume that automatic numbering for the Customer resource is not inactive in Sequence Numbers, otherwise it is a mandatory value. So the Code field is not provided. However, you always have the option to assign specific values.

These examples focus on the differences according to legislations and countries, they do not include the addition of contacts or payment conditions.

### GraphQL Mutation

```graphql
mutation ($values: CustomerCreateGLDtoInput!) {
  createCustomer(input: $values) {
    id
    code
  }
}
```

### Legislation = FR -> Country of Customer = FR

The `documentId` field corresponds to the SIRET or SIREN number. This value is not mandatory.

For the qualification of the Customer, it is recommended to assign the `vatNumber`.

```json
{
  "values": {
    "socialName": "DUPONT SA",
    "tradeName": "Dupont",
    "documentId": "323456789",
    "vatNumber": "FR33323456789",
    "addresses": [
      {
        "firstLine": "72 rue Joffre",
        "city": "PARIS",
        "zipCode": "75001",
        "countryIsoCodeAlpha2": "FR"
      }
    ]
  }
}
```

### Legislation = FR -> Country of Customer = member of VIES and not FR

The `documentId` field is not mandatory.

For the qualification of the Customer, it is recommended to assign the `vatNumber`.

For the particular case of Northern Ireland `countryIsoCodeAlpha2` should contain `XI` and not `GB`.

```json
{
  "values": {
    "socialName": "BelgiumCorp SA",
    "tradeName": "BelgiumCorp",
    "vatNumber": "BE1489052515",
    "addresses": [
      {
        "firstLine": "1 Grand Place",
        "city": "BRUXELLES",
        "zipCode": "1000",
        "countryIsoCodeAlpha2": "BE"
      }
    ]
  }
}
```

### Legislation = FR -> Country of Customer is neither FR nor VIES

The `documentId` field is not mandatory.

The `vatNumber` field is not used.

```json
{
  "values": {
    "socialName": "Smiths Corporation",
    "tradeName": "Smiths",
    "addresses": [
      {
        "firstLine": "456 Oak S",
        "city": "NEW YORK",
        "zipCode": "10001",
        "countryIsoCodeAlpha2": "US"
      }
    ]
  }
}
```

### Legislation = ES -> Country of Customer = ES

The `documentId` field corresponds to the NIF or DNI or NIE number. This value is not mandatory.

For the qualification of the Customer, it is recommended to assign the `vatNumber`.

```json
{
  "values": {
    "socialName": "Martinez SA",
    "tradeName": "Martinez",
    "documentId": "Z4660201S",
    "vatNumber": "ESZ4660201S",
    "addresses": [
      {
        "firstLine": "Calle Mayor 10",
        "city": "MADRID",
        "zipCode": "28001",
        "countryIsoCodeAlpha2": "ES"
      }
    ]
  }
}
```

### Legislation = ES -> Country of Customer = member of VIES and not ES

The `documentId` field is not mandatory.

The `vatNumber` is mandatory.

For the particular case of Northern Ireland `countryIsoCodeAlpha2` should contain `XI` and not `GB`.

```json
{
  "values": {
    "socialName": "BelgiumCorp SA",
    "tradeName": "BelgiumCorp",
    "vatNumber": "BE1489052515",
    "addresses": [
      {
        "firstLine": "1 Grand Place",
        "city": "BRUXELLES",
        "zipCode": "1000",
        "countryIsoCodeAlpha2": "BE"
      }
    ]
  }
}
```

### Legislation = ES -> Country of Customer is neither ES nor VIES

If `documentTypeId` is not mentioned, the document type "Otro documento probatorio" will be automatically filled in. Otherwise, you can provide an Id of an authorized document type (Pasaporte, Documento oficial emitido en el pais de residencia, Certificado de residencia).

The `documentId` field is not mandatory.

The `vatNumber` field is not used.

```json
{
  "values": {
    "socialName": "Smiths Corporation",
    "tradeName": "Smiths",
    "addresses": [
      {
        "firstLine": "456 Oak S",
        "city": "NEW YORK",
        "zipCode": "10001",
        "countryIsoCodeAlpha2": "US"
      }
    ]
  }
}
```

### Legislation = DE -> Country of Customer = DE

The `documentId` field corresponds to an identification number. This value is not mandatory.

For the qualification of the Customer, it is recommended to assign the `vatNumber`.

```json
{
  "values": {
    "socialName": "Musller GmbH",
    "tradeName": "Musller",
    "documentId": "80715887835",
    "vatNumber": "DE807158878",
    "addresses": [
      {
        "firstLine": "Friedrichstrasse 501",
        "city": "BERLIN",
        "zipCode": "10117",
        "countryIsoCodeAlpha2": "DE"
      }
    ]
  }
}
```

### Legislation = DE -> Country of Customer = member of VIES and not DE

The `documentId` field is not mandatory.

The `vatNumber` is mandatory.

For the particular case of Northern Ireland `countryIsoCodeAlpha2` should contain `XI` and not `GB`.

```json
{
  "values": {
    "socialName": "BelgiumCorp SA",
    "tradeName": "BelgiumCorp",
    "vatNumber": "BE1489052515",
    "addresses": [
      {
        "firstLine": "1 Grand Place",
        "city": "BRUXELLES",
        "zipCode": "1000",
        "countryIsoCodeAlpha2": "BE"
      }
    ]
  }
}
```

### Legislation = DE -> Country of Customer is neither DE nor VIES

The value of the `documentId` field is not necessary.

The `vatNumber` field is not used.

```json
{
  "values": {
    "socialName": "Smiths Corporation",
    "tradeName": "Smiths",
    "addresses": [
      {
        "firstLine": "456 Oak S",
        "city": "NEW YORK",
        "zipCode": "10001",
        "countryIsoCodeAlpha2": "US"
      }
    ]
  }
}
```

### Example of Response

```json
{
  "data": {
    "createCustomer": {
      "id": "e451aff7-0560-4e72-a6fb-4ea25e9d9c64",
      "code": "3"
    }
  }
}
```

For comprehensive insights into making precise modifications to records, check out the dedicated page on updates: Key concepts / Updating records.

## Header

| Key | Value |
|-----|-------|
| Authorization | Bearer {Current access Token} |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

## customers — Fields

### Common to customers and suppliers

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | S | Id | |
| modificationDate | DateTime | S | Modification date | |
| creationDate | DateTime | S | Creation date | |
| sourceType | GLOBAL, IMPORT_FILES, PUBLIC_API | S | Indicates the origin of the customer | |
| **General Infos** | | | | |
| code | String | N | Customer Code | 17 |
| socialName | String | * | Customer Name | 70 |
| tradeName | String | | Trade Name | 70 |
| countryAcronym | String | N | ISO2 country code | 2 |
| viesCode | String | R | VIES value prefix of the European Community VAT numbers | 2 |
| documentType | DocumentType | | Document Type (DATALOADER) | |
| documentTypeId | UUID | N | DocumentType Id | |
| documentId | String | | Identification number | 25 |
| vatNumber | String | | EU VAT Number | 25 |
| defaultAccountingAccount | AccountingAccount | | Accounting Account (DATALOADER) | |
| defaultAccountingAccountId | UUID | | Ledger general account ID | |
| businessArea | BusinessArea | | Business Area / category (DATALOADER) | |
| businessAreaId | UUID | | Business areas Id (category id) | |
| nafApeCode | String | | Business activity classification (CNAE Code for Spain) | 7 |
| comments | String | | Comments | 2000 |
| status | NONE, DISABLED, ENABLED, GDPR_APPLIED | R | Status | |
| gdprApplied | Boolean | | GDPR action | |
| gdprAppliedDate | DateTime (date only) | | GDPR date action | |
| disabled | Boolean | | Inactive | |
| disabledDate | DateTime (date only) | | Inactive date | |
| **Addresses / Contacts / Payment Conditions** | | | | |
| addresses[] | Array | * | List of addresses | |
| contacts[] | Array | | List of contacts | |
| paymentTermLines[] | Array | | List of payment terms lines | |

### Specific to customer

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| **General Infos** | | | | |
| customerType | BUSINESS, INDIVIDUAL | | Indicates the type of customer | |
| hasEquivalenceSurcharge | Boolean | | Subject to the equivalence surcharge regime | |
| specialMention | String | | Default free-text for new invoices, shown on the final invoice | 250 |
| documentLanguage | ENGLISH, FRENCH, GERMAN, SPANISH | | Language used in sales Document PDF | |
| defaultPrepaymentAccountingAccount | AccountingAccount | | Prepayment Accounting Account (DATALOADER) | |
| defaultPrepaymentAccountingAccountId | UUID | | Default Pre Payment Account ID | |
| **Sales Conditions** | | | | |
| salesDiscountGroup | SalesDiscountGroup | | Sales Discount Group (DATALOADER) | |
| salesDiscountGroupId | UUID | | Sales Discount ID | |
| salesTariff | SalesTariff | | Sales Tariff (DATALOADER) | |
| salesTariffId | UUID | | Sales Tariff ID | |
| discount | Decimal | | Discount Amount | |
| blockDeliveryNoteEnabled | Boolean | | Block Delivery Note Enabled | |
| blockInvoiceEnabled | Boolean | | Block Invoice Enabled | |
| blockOrderEnabled | Boolean | | Block Order Enabled | |
| **Spain (simplified invoice)** — live since 2026-05 | | | | |
| nonIdentifiedCustomer | Boolean | | When enabled, sales invoices for this customer are simplified invoices (no customer identification on the invoice). Requires `customerType = INDIVIDUAL`. ES only. | |
| printNameOnPdf | Boolean | | Default for the "print customer name on simplified invoice" option when creating a sales invoice for this customer. ES only. | |

### Field Info — Common fields

**code**: This field defines how the Customer is identified within the organization.
- Manual code: When manual mode is enabled, the code must be entered by the user and is a mandatory input field.
- Automatic code: When automatic mode is enabled, the code is generated by the system and cannot be edited by the user.
- Organization-specific behavior: The behavior of this field depends on the organization's configuration and may differ for customers, suppliers, and employees.
- See: API resources overview / Third Party Code Mode Settings

**countryAcronym**: ISO2 country code. This field acts as the indicator of the Customer's country.
- Same as Operating Legislation: If the Customer's country is the same as your operating legislation.
- Member of VIES: If the Customer's country is different from your operating legislation and is a member of the VAT Information Exchange System (VIES).
- Different from Legislation and VIES: If the Customer's country doesn't fall under your operating legislation and is not a member of VIES.
- In creation, this field may be optional because its value is automatically determined as follows:
  - If explicitly provided, this value is used.
  - Otherwise, it is derived from the `countryIsoCodeAlpha2` of the address, or from the `isoCodeAlpha2` of the country linked to the `countryId` if provided.
  - If neither of these fields is specified in the address, the value defaults to the country of the current legislation (FR, ES, or DE).

**documentTypeId**: Allows you to define the type of identification document for the Customer. In creation, it is automatically set by default based on the legislation and country, and, with few exceptions, does not need to be filled in during creation. Otherwise, to assign a value, retrieve the desired ID from document type.

#### Document Types by Legislation — FR

| Country | Allowed document types (code name) |
|---------|------------------------------------|
| FR | 01 SIRET |
| VIES not FR | 02 TVA Intracommunautaire |
| Other | 06 Autre document justificatif |

#### Document Types by Legislation — ES

| Country | Allowed document types (code name) |
|---------|------------------------------------|
| ES | 01 NIF/DNI |
| VIES not ES | 02 NIF/IVA |
| Other | 03 Pasaporte |
| Other | 04 Documento oficial emitido en el pais de residencia |
| Other | 05 Certificado de residencia |
| Other | 06 Otro documento probatorio |

#### Document Types by Legislation — DE

| Country | Allowed document types (code name) |
|---------|------------------------------------|
| DE | 01 Steur-IdN |
| VIES not DE | 02 USt-IdNr |
| Other | 06 Another supporting document |

**documentId**: Identification number, value formats may differ depending on legislation.
- For a French Customer, you can fill any SIREN of 9 characters or any SIRET of 14 characters. The value is not mandatory.
- For a German Customer, you may enter either a Steuer-IdNr with exactly 11 characters or another value ranging from 11 to 14 characters. The value is not mandatory.
- For a Spanish Customer, you need to fill a valid NIF/DNI/NIE number. The value is not mandatory.

**vatNumber**: An EU VAT Number is a unique identifier for tax purposes within the European Union, used primarily for cross-border transactions. It starts with a two-letter country code followed by a series of numbers and sometimes letters, varying by country.
- This field is not applicable for Customers that are not registered in the VIES system.
- For VIES Customers, the format is described at FAQ N11: https://ec.europa.eu/taxation_customs/vies/#/faq
- **FR legislation**:
  - If the Customer is FR, the VAT number is recommended but not mandatory.
  - If the Customer is VIES but not FR, the VAT number is recommended but not mandatory.
  - If the Customer is neither VIES nor FR, the VAT number is not used.
- **ES legislation**:
  - If the Customer is ES, the VAT number is recommended but not mandatory.
  - If the Customer is VIES but not ES, the VAT number is recommended but not mandatory.
  - If the Customer is neither VIES nor ES, the VAT number is not used.
- **DE legislation**:
  - If the Customer is DE, the VAT number is recommended but not mandatory.
  - If the Customer is VIES but not DE, the VAT number is recommended but not mandatory.
  - If the Customer is neither VIES nor DE, the VAT number is not used.

**code**: Code of the Customer. During creation, it is automatically calculated if the option is not inactive in Sequence Numbers, otherwise it is a mandatory value. In any cases, it is still possible to assign it to enforce a specific value.
- Forcing a value allows you to retrieve an alphanumeric code that can already identify the Customer, even if automatic numbering mode is active.
- For DE legislation, automatic numbering is mandatory and cannot be disabled from Sequence Numbers.
- If you force a value, it must be strictly numeric and adhere to the rule of a number being between 10000 and 69999 for customers, and between 70000 and 99999 for suppliers or employees.

**tradeName**: Can be used to fill in a shorter name than socialName.

**disabled**: If true the third party can no longer be used, but will remain in the existing information.

**defaultAccountingAccountId**: Id of the general account in accountingAccount associated with the Customer. The subAccountType of the account must be CUSTOMER. In creation, if defaultAccountingAccountId is not specified, the account defined in Accounting Configuration will be automatically assigned.
- Caution: as per DE legislation, assigning a defaultAccountingAccountId is mandatory. If a default value is not provided, it will then be necessary to specify in defaultAccountingAccountId the Customer collective account ID.

### Field Info — Specific to customers

**customerType**: This field distinguishes between business customers (such as companies and sole traders) and private individual customers. If not specified at creation, the value defaults to BUSINESS.

**specialMention**: Default free-text field defined at customer level, intended to appear on the final invoice. In the application UI, it is automatically pre-filled when creating a new invoice. However, via the API, this value is not automatically assigned: if you want to create an invoice including the customer's mention, you must first retrieve it from the customer entity and then explicitly set it in the specialMention field of the sales invoice.

**documentLanguage**: Applies to section titles and column headers in sales documents PDFs.

**defaultPrepaymentAccountingAccountId**: This accounting code is used to post the accounting entry to an advance payment and deposit received account for advance invoices.

**blockDeliveryNoteEnabled**: Indicates whether the delivery note is blocked for this particular sales order. When set to true, delivery notes cannot be generated.

**blockInvoiceEnabled**: Indicates whether invoices can be generated for this particular sales order. When set to true, invoices cannot be generated.

**blockOrderEnabled**: Indicates whether new orders can be added to this particular sales instance. When set to true, new orders cannot be added.

**hasEquivalenceSurcharge**:
- Unused for the French market.
- Indicates whether a customer is subject to the equivalence surcharge regime.
- The equivalence surcharge, as applied in Spain, is a form of simplified VAT for retail traders who cannot recover VAT.
- The rates of the equivalence surcharge vary according to the VAT rate applicable to the products, with surcharge rates of 5.2% for products with a general VAT of 21%, 1.4% for products with a reduced VAT of 10%, and other rates for different products or categories.
- Unused for the German market.

## customers/addresses — Fields

### Common to customers, suppliers

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | S | Id | |
| creationDate | DateTime | S | Creation Date | |
| modificationDate | DateTime | S | Modification Date | |
| countryId | UUID | | Country Id | |
| country | Country | | Country (DATALOADER) | |
| countryIsoCodeAlpha2 | String | | Country Code | 2 |
| countryName | String | R | CountryName | |
| name | String | | Code | 50 |
| firstLine | String | | First Line | 35 |
| secondLine | String | | Second Line | 35 |
| city | String | | Town | 35 |
| zipCode | String | | Postal Code | 9 |
| province | String | | Province / Region / Federal State | 25 |
| isMainAddress | Boolean | R | Is the main address | |

### Specific to customers

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| isDefaultDeliveryAddress | Boolean | | Is the default delivery/shipping Address | |
| isDeliveryAddress | Boolean | R | Is a delivery/shipping Address | |

### Field Info — Addresses

**countryIsoCodeAlpha2**: ISO2 country code. This field can be used for creation and serves as a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.

**isDefaultDeliveryAddress**: Indicates whether the address is the default delivery/shipment address, which is automatically suggested as the shipping address when entering a sales quote.

**isDeliveryAddress**: In the current version, all addresses are potentially delivery addresses. It is possible to change the delivery address during the sales quote entry process.

## customers/contacts — Fields

### Common to customers, suppliers, employees

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | S | Id | |
| creationDate | DateTime | S | Creation Date | |
| modificationDate | DateTime | S | Modification Date | |
| isDefault | Boolean | Calc | Main contact (computed, not filterable or sortable) | |
| courtesy | NONE, BLANK, MISS, MR | | Courtesy | |
| name | String | | First name (John) | 35 |
| surname | String | | Family name (Smith) | 35 |
| jobName | String | | Job name | 35 |
| jobArea | JobArea | | Job Area (DATALOADER) | |
| jobAreaId | UUID | | Job areas Id | |
| emails[] | Array | | List of emails | |
| phones[] | Array | | List of phones | |
| socialMedias[] | Array | | List Social Networking | |

### Specific to customers

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| isDefaultCustomer | Boolean | | Main contact (Filterable / sortable) | |

## customers/contacts/emails — Fields

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | S | Id | |
| creationDate | DateTime | S | Creation Date | |
| modificationDate | DateTime | S | Modification Date | |
| emailAddress | String | | Email address | 70 |
| usage | EMPTY, INVOICES, NOT_SET, OTHERS, PAYMENTS | | Type | |
| isDefault | Boolean | | Main mail | |

## customers/contacts/phones — Fields

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | S | Id | |
| creationDate | DateTime | S | Creation Date | |
| modificationDate | DateTime | S | Modification Date | |
| number | String | | Phone | 20 |
| type | EMPTY, FAX, LANDLINE, MOBILE, NOT_SET, SKYPE, WHATS_APP | | Type | |
| isDefault | Boolean | | Main phone | |

## customers/contacts/socialMedias — Fields

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | S | UUID | |
| creationDate | DateTime | S | Creation Date | |
| modificationDate | DateTime | S | Modification Date | |
| name | String | | Name | 30 |
| link | String | | Link | 100 |

## customers/paymentTermLines — Fields

### Common to customers, suppliers

Payment terms, used to define the list of payment terms for Customer.

Example:
- Advance payment by check 30%
- 30% by transfer at 30 days net
- The balance of 40% by bank transfer at 60 days end of month

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | S | Id |
| creationDate | DateTime | S | Creation Date |
| modificationDate | DateTime | S | Modification Date |
| order | Int | * | Classification of lines from 0 to n |
| paymentMean | PaymentMean | | Payment Mean (DATALOADER) |
| paymentMeanId | UUID | | Id of the payment mean |
| type | NONE, FIXED_AMOUNT, PERCENTAGE, REMAINING_AMOUNT | | Type of calculation for the payment term line |
| value | Decimal | * | Numerical value used based on the type of calculation |
| condition | NONE, DAY_OF_PAYMENT, END_OF_MONTH | | To calculate due date |
| day | Int | | Number of days |
| payDays[] | Int | | Array of optional due days |

### Field Info — Payment Terms

**order**: If REMAINING_AMOUNT, must always be last with the value 9999.

**payDays**: Array of optional due days. The day contained in the array closest to the number of days defined in `day` will be used to calculate the Due Date.

**type**: Defines the method used for payment term calculations. The interpretation of `value` depends on the selected type:
- `NONE`: No specific calculation is applied.
- `PERCENTAGE`: `value` represents a percentage of the total amount.
- `FIXED_AMOUNT`: `value` is a fixed monetary amount.
- `REMAINING_AMOUNT`: The remaining balance after all prior calculations. Must always be last in the order.

**value**: Represents the numerical input for the calculation based on type.
- If type = PERCENTAGE, value should be between 0 and 100, indicating the percentage of the total amount.
- If type = FIXED_AMOUNT, value is a fixed currency amount (e.g., 500 means a fixed payment of 500).
- If type = REMAINING_AMOUNT, value is ignored, as the system automatically calculates the remaining balance.

Examples:
- If type = PERCENTAGE and value = 30, the payment term will cover 30% of the total amount.
- If type = FIXED_AMOUNT and value = 500, the payment term will apply a fixed amount of 500.
- If type = REMAINING_AMOUNT, the system will allocate the remaining balance after all prior lines are processed. In this case, order must always be set to 9999.

---
