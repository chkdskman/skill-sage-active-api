# Suppliers — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/suppliers

## HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | createSupplier | SupplierCreateGLDtoInput |
| POST | Update | Mutation | updateSupplier | SupplierUpdateGLDtoInput |
| POST | Delete | Mutation | deleteSupplier | SupplierDeleteGLDtoInput |
| POST | Read | Query | suppliers filtered by id | — |
| POST | List | Query | suppliers | — |

## Description

Supplier management. Allows the identification of a third-party Supplier within an organization.

It offers the option to add contacts within the Supplier and their details, as well as define the Supplier's default payment conditions.

Every Supplier must have a code, which, depending on the organization's settings, can be manually entered or automatically generated. In the DE (German) legislation, automatic numbering is mandatory.

## Creating a Supplier

Creating a Supplier record involves multiple variables, including the legislation (FR, ES, or DE) and the country of the Supplier.

### Legislation-Specific Fields

Certain mandatory fields will depend on the legislation you are operating under (FR, ES, or DE).

### Country of the Supplier (countryAcronym)

Understanding the country of the Supplier is crucial as it introduces additional variables that can affect the mandatory and optional fields needed for Supplier creation.

- **Same as Operating Legislation**: If the Supplier's country is the same as your operating legislation.
- **Member of VIES**: If the Supplier's country is different from your operating legislation and is a member of the VAT Information Exchange System (VIES).
- **Different from Legislation and VIES**: If the Supplier's country doesn't fall under your operating legislation and is not a member of VIES.

### Common Rules

- `countryIsoCodeAlpha2` is a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.
- `code` is optional if the automatic numbering option (Sequence number) is enabled for the Supplier resource. However, it can be set to enforce a specific value instead of requesting the assignment of a new sequential number.
- `documentType` allows you to define the type of identification document for the Supplier. It is automatically set by default based on the legislation and country, and, with few exceptions, does not need to be filled in during creation.

## Examples of Creation

Below, for each legislation (FR, ES, or DE), the specific rules and differences depending on the Supplier's country. These examples assume that automatic numbering for the Supplier resource is not inactive in Sequence Numbers, otherwise it is a mandatory value. So the Code field is not provided. However, you always have the option to assign specific values.

These examples focus on the differences according to legislations and countries, they do not include the addition of contacts or payment conditions.

### GraphQL Mutation

```graphql
mutation ($values: SupplierCreateGLDtoInput!) {
  createSupplier(input: $values) {
    id
    code
  }
}
```

### Legislation = FR -> Country of Supplier = FR

The `documentId` field corresponds to the SIRET or SIREN number. This value is not mandatory.

For the qualification of the Supplier, it is recommended to assign the `vatNumber`.

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

### Legislation = FR -> Country of Supplier = member of VIES and not FR

The `documentId` field is not mandatory.

For the qualification of the Supplier, it is recommended to assign the `vatNumber`.

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

### Legislation = FR -> Country of Supplier is neither FR nor VIES

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

### Legislation = ES -> Country of Supplier = ES

The `documentId` field corresponds to the NIF or DNI or NIE number. This value is not mandatory.

For the qualification of the Supplier, it is recommended to assign the `vatNumber`.

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

### Legislation = ES -> Country of Supplier = member of VIES and not ES

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

### Legislation = ES -> Country of Supplier is neither ES nor VIES

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

### Legislation = DE -> Country of Supplier = DE

The `documentId` field corresponds to an identification number. This value is not mandatory.

For the qualification of the Supplier, it is recommended to assign the `vatNumber`.

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

### Legislation = DE -> Country of Supplier = member of VIES and not DE

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

### Legislation = DE -> Country of Supplier is neither DE nor VIES

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
    "createSupplier": {
      "id": "e451aff7-0560-4e72-a6fb-4ea25e9d9c64",
      "code": "3"
    }
  }
}
```

Explore Efficient Record Updates: For comprehensive insights into making precise modifications to records, check out the dedicated page on updates: Key concepts / Updating records.

## Header

| Key | Value |
|-----|-------|
| Authorization | Bearer {Current access Token} |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

## suppliers

### Fields (Common to customers and suppliers)

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | System | Id | — |
| modificationDate | DateTime | System | Modification date | — |
| creationDate | DateTime | System | Creation date | — |
| sourceType | GLOBAL, IMPORT_FILES, PUBLIC_API | System | Indicates the origin of the supplier | — |
| code | String | Non-modifiable | Supplier Code | 17 |
| socialName | String | Mandatory | Supplier Name | 70 |
| tradeName | String | Optional | Trade Name | 70 |
| countryAcronym | String | Non-modifiable | ISO2 country code | 2 |
| viesCode | String | Read-only | VIES value prefix of the European Community VAT numbers | 2 |
| documentType | DocumentType | Optional | Fields of DocumentType (DATALOADER) | — |
| documentTypeId | UUID | Non-modifiable | DocumentType Id | — |
| documentId | String | Optional | Identification number | 25 |
| vatNumber | String | Optional | EU VAT Number | 25 |
| defaultAccountingAccount | AccountingAccount | Optional | Fields of AccountingAccount (DATALOADER) | — |
| defaultAccountingAccountId | UUID | Optional | Ledger general account ID | — |
| businessArea | BusinessArea | Optional | Fields of BusinessArea/category (DATALOADER) | — |
| businessAreaId | UUID | Optional | Business areas Id (category id) | — |
| nafApeCode | String | Optional | Business activity classification (CNAE Code for Spain) | 7 |
| comments | String | Optional | Comments | 2000 |
| status | NONE, DISABLED, ENABLED, GDPR_APPLIED | Read-only | Status | — |
| gdprApplied | Boolean | Optional | GDPR action | — |
| gdprAppliedDate | DateTime | Optional (date only) | GDPR date action | — |
| disabled | Boolean | Optional | Inactive | — |
| disabledDate | DateTime | Optional (date only) | Inactive date | — |
| addresses[] | Array | Mandatory | List of addresses | — |
| contacts[] | Array | Optional | List of contacts | — |
| paymentTermLines[] | Array | Optional | List of payment terms lines | — |

### Fields (Specific to suppliers)

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| defaultPurchaseAccount | AccountingAccount | Optional | Fields of Purchase Account (DATALOADER) | — |
| defaultPurchaseAccountId | UUID | Optional | Purchase Account Id | — |
| vatCriterion | Boolean | Optional | Enable Cash VAT | — |
| personalIncomeTax | Boolean | Optional | Indicates if income tax is applied | — |
| taxTreatmentId | UUID | Optional | TaxTreatment ID | — |
| taxTreatment | TaxTreatment | Optional | Field of taxTreatment | — |
| transactionCategory | Category | Optional | Fields of TransactionCategory (DATALOADER) | — |
| transactionCategoryId | UUID | Optional | Transaction category used to determine default accounting settings | — |

### Field Info

**code**: This field defines how the Supplier is identified within the organization.
- Manual code: When manual mode is enabled, the code must be entered by the user and is a mandatory input field.
- Automatic code: When automatic mode is enabled, the code is generated by the system and cannot be edited by the user.
- Organization-specific behavior: The behavior of this field depends on the organization's configuration and may differ for customers, suppliers, and employees.

**countryAcronym**: ISO2 country code. This field acts as the indicator of the Supplier's country.
- Same as Operating Legislation: If the Supplier's country is the same as your operating legislation.
- Member of VIES: If the Supplier's country is different from your operating legislation and is a member of the VAT Information Exchange System (VIES).
- Different from Legislation and VIES: If the Supplier's country doesn't fall under your operating legislation and is not a member of VIES.
- In creation, this field may be optional because its value is automatically determined as follows:
  - If explicitly provided, this value is used.
  - Otherwise, it is derived from the `countryIsoCodeAlpha2` of the address, or from the `isoCodeAlpha2` of the country linked to the `countryId` if provided.
  - If neither of these fields is specified in the address, the value defaults to the country of the current legislation (FR, ES, or DE).

**documentTypeId**: Allows you to define the type of identification document for the Supplier. In creation, it is automatically set by default based on the legislation and country, and, with few exceptions, does not need to be filled in during creation. Otherwise, to assign a value, retrieve the desired ID from document type.

#### Document Types by Legislation

**FR:**

| Country | Allowed document types (code name) |
|---------|-----------------------------------|
| FR | 01 SIRET |
| VIES not FR | 02 TVA Intracommunautaire |
| Other | 06 Autre document justificatif |

**ES:**

| Country | Allowed document types (code name) |
|---------|-----------------------------------|
| ES | 01 NIF/DNI |
| VIES not ES | 02 NIF/IVA |
| Other | 03 Pasaporte |
| Other | 04 Documento oficial emitido en el pais de residencia |
| Other | 05 Certificado de residencia |
| Other | 06 Otro documento probatorio |

**DE:**

| Country | Allowed document types (code name) |
|---------|-----------------------------------|
| DE | 01 Steur-IdN |
| VIES not DE | 02 USt-IdNr |
| Other | 06 Another supporting document |

**documentId**: Identification number, value formats may differ depending on legislationCode.
- For a French Supplier, you can fill any SIREN of 9 characters or any SIRET of 14 characters. The value is not mandatory.
- For a German Supplier, you may enter either a Steuer-IdNr with exactly 11 characters or another value ranging from 11 to 14 characters. The value is not mandatory.
- For a Spanish Supplier, you need to fill a valid NIF/DNI/NIE number. The value is not mandatory.

**vatNumber**: An EU VAT Number is a unique identifier for tax purposes within the European Union, used primarily for cross-border transactions. It starts with a two-letter country code followed by a series of numbers and sometimes letters, varying by country.
- This field is not applicable for Suppliers that are not registered in the VIES system.
- For VIES Suppliers, the format is described at https://ec.europa.eu/taxation_customs/vies/#/faq (FAQ N.11)
- FR legislation:
  - If the Supplier is FR, the VAT number is recommended but not mandatory.
  - If the Supplier is VIES but not FR, the VAT number is recommended but not mandatory.
  - If the Supplier is neither VIES nor FR, the VAT number is not used.
- ES legislation:
  - If the Supplier is ES, the VAT number is recommended but not mandatory.
  - If the Supplier is VIES but not ES, the VAT number is recommended but not mandatory.
  - If the Supplier is neither VIES nor ES, the VAT number is not used.
- DE legislation:
  - If the Supplier is DE, the VAT number is recommended but not mandatory.
  - If the Supplier is VIES but not DE, the VAT number is recommended but not mandatory.
  - If the Supplier is neither VIES nor DE, the VAT number is not used.

**code**: Code of the Supplier. During creation, it is automatically calculated if the option is not inactive in Sequence Numbers, otherwise it is a mandatory value. In any cases, it is still possible to assign it to enforce a specific value.
- FR/ES: Forcing a value allows you to retrieve an alphanumeric code that can already identify the Supplier, even if automatic numbering mode is active.
- DE: Automatic numbering is mandatory and cannot be disabled from Sequence Numbers. If you force a value, it must be strictly numeric and adhere to the rule of a number being between 10000 and 69999 for customers, and between 70000 and 99999 for suppliers or employees.

**tradeName**: Can be used to fill in a shorter name than socialName.

**disabled**: If true the third party can no longer be used, but will remain in the existing information.

**defaultAccountingAccountId**: Id of the general account in accountingAccount associated with the Supplier. The subAccountType of the account must be PROVIDER. In creation, if defaultAccountingAccountId is not specified, the account defined in Accounting Configuration will be automatically assigned. Caution: as per DE legislation, assigning a defaultAccountingAccountId is mandatory; if a default value is not provided, it will then be necessary to specify in defaultAccountingAccountId the Supplier collective account ID.

#### Specific to Suppliers Info

**vatCriterion**: Enable Cash VAT. Cash VAT is the scheme applied to services provided by service providers. The implementation of Cash VAT allows automatic consideration of the tax when performing entries and feeds the VAT return. Used only for France.

**personalIncomeTax**: Enable Income Tax. Used only for Spain. Indicates whether personal income tax is applicable for the supplier. If true, personal income tax handling is enabled.

**taxTreatmentId**: Tax treatment applicable. Used only for Spain. References the tax treatment applicable to the supplier. Links to a TaxTreatment object that defines the correct tax handling rules.

**transactionCategoryId**: This field allows selecting a transaction category of type PURCHASE to automatically determine default accounting settings. Each transaction category is associated with a default purchase account and a tax treatment. It can be used during supplier creation or update to simplify the assignment of the defaultPurchaseAccountId and the taxTreatmentId.

## suppliers/addresses

Common to customers, suppliers.

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | System | Id | — |
| creationDate | DateTime | System | Creation Date | — |
| modificationDate | DateTime | System | Modification Date | — |
| countryId | UUID | Optional | Country Id | — |
| country | Country | Optional | Fields of Country (DATALOADER) | — |
| countryIsoCodeAlpha2 | String | Optional | Country Code | 2 |
| countryName | String | Read-only | CountryName | — |
| name | String | Optional | Code | 50 |
| firstLine | String | Optional | First Line | 35 |
| secondLine | String | Optional | Second Line | 35 |
| city | String | Optional | Town | 35 |
| zipCode | String | Optional | Postal Code | 9 |
| province | String | Optional | Province / Region / Federal State | 25 |
| isMainAddress | Boolean | Read-only | Is the main address | — |

### Address Field Info

**countryIsoCodeAlpha2**: ISO2 country code. This field can be used for creation and serves as a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.

## suppliers/contacts

Common to customers, suppliers, employees.

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | System | Id | — |
| creationDate | DateTime | System | Creation Date | — |
| modificationDate | DateTime | System | Modification Date | — |
| isDefault | Boolean | Computed | Main contact (computed, not filterable or sortable) | — |
| courtesy | NONE, BLANK, MISS, MR | Optional | Courtesy | — |
| name | String | Optional | First name (John) | 35 |
| surname | String | Optional | Family name (Smith) | 35 |
| jobName | String | Optional | Job name | 35 |
| jobArea | JobArea | Optional | Fields of JobArea (DATALOADER) | — |
| jobAreaId | UUID | Optional | Job areas Id | — |
| emails[] | Array | Optional | List of emails | — |
| phones[] | Array | Optional | List of phones | — |
| socialMedias[] | Array | Optional | List Social Networking | — |

### Fields (Specific to suppliers)

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| isDefaultSupplier | Boolean | Optional | Main contact (Filterable / sortable) | — |

## suppliers/contacts/emails

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | System | Id | — |
| creationDate | DateTime | System | Creation Date | — |
| modificationDate | DateTime | System | Modification Date | — |
| emailAddress | String | Optional | Email address | 70 |
| usage | EMPTY, INVOICES, NOT_SET, OTHERS, PAYMENTS | Optional | Type | — |
| isDefault | Boolean | Optional | Main mail | — |

## suppliers/contacts/phones

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | System | Id | — |
| creationDate | DateTime | System | Creation Date | — |
| modificationDate | DateTime | System | Modification Date | — |
| number | String | Optional | Phone | 20 |
| type | EMPTY, FAX, LANDLINE, MOBILE, NOT_SET, SKYPE, WHATS_APP | Optional | Type | — |
| isDefault | Boolean | Optional | Main phone | — |

## suppliers/contacts/socialMedias

| Field | Type | Required | Description | Length |
|-------|------|----------|-------------|--------|
| id | UUID | System | UUID | — |
| creationDate | DateTime | System | Creation Date | — |
| modificationDate | DateTime | System | Modification Date | — |
| name | String | Optional | Name | 30 |
| link | String | Optional | Link | 100 |

## suppliers/paymentTermLines

Common to customers, suppliers. Payment terms, used to define the list of payment terms for Supplier.

Example:
- Advance payment by check 30%
- 30% by transfer at 30 days net
- The balance of 40% by bank transfer at 60 days end of month

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| order | Int | Mandatory | Classification of lines from 0 to n |
| paymentMean | PaymentMean | Optional | Fields of PaymentMean (DATALOADER) |
| paymentMeanId | UUID | Optional | Id of the payment mean |
| type | NONE, FIXED_AMOUNT, PERCENTAGE, REMAINING_AMOUNT | Optional | Type of calculation for the payment term line |
| value | Decimal | Mandatory | Numerical value used based on the type of calculation |
| condition | NONE, DAY_OF_PAYMENT, END_OF_MONTH | Optional | To calculate due date |
| day | Int | Optional | Number of days |
| payDays[] | Int | Optional | Array of Optional due days |

### Payment Term Info

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
