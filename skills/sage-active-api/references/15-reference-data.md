# Reference Data — Organizations, Users, Countries, Taxes, Banks, Config

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/organizations
> - https://developer.sage.com/sageactive/resources/userprofile
> - https://developer.sage.com/sageactive/resources/users
> - https://developer.sage.com/sageactive/resources/useraccesspolicycheck
> - https://developer.sage.com/sageactive/resources/countries
> - https://developer.sage.com/sageactive/resources/zipcodes
> - https://developer.sage.com/sageactive/resources/currencies
> - https://developer.sage.com/sageactive/resources/documenttypes
> - https://developer.sage.com/sageactive/resources/unitofmeasurement
> - https://developer.sage.com/sageactive/resources/taxes
> - https://developer.sage.com/sageactive/resources/taxgroups
> - https://developer.sage.com/sageactive/resources/taxtreatments
> - https://developer.sage.com/sageactive/resources/bankaccounts
> - https://developer.sage.com/sageactive/resources/paymentmeans
> - https://developer.sage.com/sageactive/resources/paymentmethods
> - https://developer.sage.com/sageactive/resources/paymentterms
> - https://developer.sage.com/sageactive/resources/businessareas
> - https://developer.sage.com/sageactive/resources/jobareas
> - https://developer.sage.com/sageactive/resources/dimensions
> - https://developer.sage.com/sageactive/resources/transactioncategories
> - https://developer.sage.com/sageactive/resources/accountingexercises
> - https://developer.sage.com/sageactive/resources/accountingjournaltypes
> - https://developer.sage.com/sageactive/resources/thirdpartycodemodesettings
> - https://developer.sage.com/sageactive/resources/organizationaccountingsetup
> - https://developer.sage.com/sageactive/resources/organizationsalessetup
> - https://developer.sage.com/sageactive/resources/emailtemplates
> - https://developer.sage.com/sageactive/resources/operationalnumberpresettexts
> - https://developer.sage.com/sageactive/resources/getlocalizedenumvalues
> - https://developer.sage.com/sageactive/resources/getlocalizederrormessage
> - https://developer.sage.com/sageactive/resources/communication

---

## Organizations

> 🚧 **COMING SOON — `organizations` / `organizationDetail` split** — announced 2026-06 — [migration guide](https://developer.sage.com/sageactive/resources/organizations_new)
> In the next Sage release, `organizations` (LIST and READ by id) will return only the reduced set of fields used to select a business: `id`, `creationDate`, `modificationDate`, `onboardingCompleted`, `onboardingDateCompleted`, `legislationCode`, `socialName`.
> The full organization configuration (identification, tax settings, contacts, addresses, email settings, taxOfficialModels) moves to a new **`organizationDetail`** READ query, resolved for the organization set in `X-OrganizationId`.
> To limit breaking changes, `organizations` will keep exposing today's fields, but the ones that move to `organizationDetail` will return `null`. If you read any of those fields and use their values, migrate to `organizationDetail` (call it after selecting the organization from the list).
> Note: on the new method page, `legislationCode` is documented as `FR, ES, DE or PT` — Portuguese legislation support is being introduced.

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | organizations filtered by id |
| POST | List | Query | organizations |
| POST | Read | Query | organizationDetail 🚧 COMING SOON (announced 2026-06) |

### Description

The application can be used to manage different establishments or subsidiaries, called Organization in the API context and Company or Business in the product interface.
Each organization is represented separately within the application, allowing for individualized handling and customization.
The current working organization's name is displayed in the top right title bar, providing clear context and navigation as you interact with the different aspects of the organization.

Organizations is used to get the list of organizations authorized for the current user (the one authenticated for access to the public API).
From this list, you can select the desired organization in order to access its data.
To do so, the list of organizations provides the fields X-OrganizationId and X-TenantId, which must be included in the headers of any request where the data is organization-specific.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| x-api-key | Primary or secondary subscription key of your app |

### organizations Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | System field that cannot be assigned |
| creationDate | DateTime | System | Creation date |
| modificationDate | DateTime | System | Modification date |
| tenantId | UUID | - | **Deprecated.** Tenant in which the organization is located |
| onboardingCompleted | Boolean | - | Indicates if the onboarding is completed |
| onboardingDateCompleted | DateTime | - | Date when the onboarding was completed (date-only) |
| legislationCode | String | - | Compliance with the legislation of the organization |
| socialName | String(50) | - | Business name |
| documentId | String(14) | - | Identification number |
| documentTypeId | UUID | - | Id document type |
| allowBlankIdentificationNumbers | Boolean | - | always True |
| useCustomerCodes | Boolean | - | always True |
| useWithholdingForSales | Boolean | - | Indicates whether withholding tax is applied on sales |
| useWithholdingTaxTreatmentId | UUID | - | Tax treatment used for sales withholding tax (added 2026-06) |
| nafApeCode | String(7) | - | NAF/APE Code |
| vatNumber | String(25) | - | Intracommunity VAT number |
| useThirdPartyBilling | Boolean | - | Indicates whether third-party billing is used (added 2026-06, PT legislation; unused for FR) |
| thirdPartyBillingName | String | - | Third-party billing name (added 2026-06, PT legislation; unused for FR) |
| thirdPartyBillingVatNumber | String | - | Third-party billing VAT number (added 2026-06, PT legislation; unused for FR) |
| vatCriterion | Boolean | - | Enable Cash VAT |
| datevConsultantNumber | Int | - | DATEV consultant number |
| datevClientNumber | Int | - | DATEV client number |
| taxAuditExportNotes | String(150) | - | Notes related to tax audit export |
| currency | Currency | - | Fields of Currency (DATALOADER) |
| currencyId | UUID | - | Currency ID (default value auto-calculated on creation) |
| contacts[] | Array | - | Contacts organization |
| addresses[] | Array | - | Addresses organization |
| taxOfficialModelsOrganization[] | Array | - | List of taxOfficialModel |
| emailSubject | String(200) | - | The text that appears in the email header |
| emailMessage | String(2500) | - | The text that appears in the email body |
| sendEmailCopy | Boolean | - | Define if a copy email is sent |

### Info

- **onboardingCompleted:** Indicates whether the onboarding process for the organization has been fully completed. The API automatically checks this flag before allowing interactions with the organization's data. If the flag is false the organizationId and tenantId will be set to an empty GUID, preventing any use of the organization with the public API.
- **legislationCode:** FR, ES, or DE (PT is added with the upcoming `organizationDetail` split — the new-method page documents `FR, ES, DE or PT`). This variable can be used if your application needs to work for different legislations, to account for the differences between them. Please note that this cannot be set during the organization creation process as the organization inherits the legislation from the tenant.
- **documentTypeId:** Id of an allowed document type, values are different depending on legislationCode:

| Country | Allowed document types (code name) |
|---------|-------------------------------------|
| FR | 01 SIREN / SIRET |
| DE | 01 Steur-IdN |
| ES | 01 NIF/DNI |

- **documentId:** Identification number, please note that each organization has a unique value. SIREN of 9 characters or any SIRET of 14 characters. Steur-Idn of 11 characters. Valid NIF number.
- **useWithholdingForSales:** Unused for FR and DE. For ES, indicates whether withholding tax (IRPF) must be applied on sales documents for the organization. When enabled, sales invoices may include withholding tax according to Spanish fiscal rules. See also `organizationIrpfSetupByOrgId` for the configured IRPF rate.
- **useWithholdingTaxTreatmentId:** Tax treatment used for sales withholding tax. Marked "Unused" for the French market in the official docs.
- **useThirdPartyBilling / thirdPartyBillingName / thirdPartyBillingVatNumber:** Third-party billing settings introduced for the Portuguese legislation. Unused for the French market.
- **vatCriterion:** Enable Cash VAT. When registering your business and depending on its type of activity, you can configure the type of VAT that will be automatically taken into account when performing entries. Used only for France and Germany.
- **datevConsultantNumber:** This is the identification number of the DATEV consultant. It is assigned to the tax advisor or accounting firm managing a company's accounting within DATEV, a widely used software in Germany. Not used (only for German legislation).
- **datevClientNumber:** This field represents the DATEV client identification number. It is assigned to each company registered in DATEV and helps associate accounting entries and financial transactions with the correct organization in the system. Not used (only for German legislation).
- **taxAuditExportNotes:** This field allows adding specific notes to tax audit exports. These notes can be used to include accounting or regulatory details relevant to the organization when generating export files. Not used (only for German legislation).

### organizations/addresses Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | System field that cannot be assigned |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| countryId | UUID | - | Country Id |
| country | Country | - | Fields of Country (DATALOADER) |
| countryIsoCodeAlpha2 | String(2) | - | Country Code |
| countryName | String | Read-only | Country Name |
| name | String(50) | - | Code |
| firstLine | String(35) | - | First Line |
| secondLine | String(35) | - | Second Line |
| city | String(35) | - | Town |
| zipCode | String(9) | - | Postal Code |
| province | String(25) | - | Province / Region / Federal State |
| isMainAddress | Boolean | Read-only | Is the main address |

**Info:** countryIsoCodeAlpha2 - ISO2 country code. This field can be used for creation and serves as a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.

### organizations/contacts Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | System field that cannot be assigned |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| isDefault | Boolean | Computed | Main contact (computed, not filterable or sortable) |
| courtesy | Enum | - | Values: NONE, BLANK, MISS, MR |
| name | String(35) | - | First name (John) |
| surname | String(35) | - | Family name (Smith) |
| jobName | String(35) | - | Job name |
| jobArea | JobArea | - | Fields of JobArea (DATALOADER) |
| jobAreaId | UUID | - | Job areas Id |
| emails[] | Array | - | List of emails |
| phones[] | Array | - | List of phones |
| socialMedias[] | Array | - | List Social Networking |

### organizations/contacts/emails Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | System field that cannot be assigned |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| emailAddress | String(70) | - | Email address |
| usage | Enum | - | Values: EMPTY, INVOICES, NOT_SET, OTHERS, PAYMENTS |
| isDefault | Boolean | - | Main mail |

### organizations/contacts/phones Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | System field that cannot be assigned |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| number | String(20) | - | Phone |
| type | Enum | - | Values: EMPTY, FAX, LANDLINE, MOBILE, NOT_SET, SKYPE, WHATS_APP |
| isDefault | Boolean | - | Main phone |

### organizations/contacts/socialMedias Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | System field that cannot be assigned |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| name | String(30) | - | Name |
| link | String(100) | - | Link |

### organizations/taxOfficialModelsOrganization Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | System field that cannot be assigned |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| initialDate | DateTime | - | Start date |
| finalDate | DateTime | - | End date |
| periodicity | Enum | - | Values: NONE, ANNUAL, DAILY, MONTHLY, QUARTERLY |
| territory | Enum | - | Values: NONE, COMMON, STATE |
| description | String(200) | - | Description |

---

## User Profile

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | userProfile |

### Description

Represents the information about the currently authenticated user within the system. This includes language preferences, name, email.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| x-api-key | Primary or secondary subscription key of your app |

### userProfile Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System, Read-only | Id |
| firstName | String | Read-only | User's first name (live since 2026-06) |
| lastName | String | Read-only | User's last name (live since 2026-06) |
| fullName | String | Read-only | User's full name |
| applicationLanguageCode | String | Read-only | User's language (e.g. en-US) |
| authenticationEmail | String | Read-only | User's email |

**Info:** applicationLanguageCode - Allows you to know the language preference of the connected user to also localize your application.

---

## Users

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | users |

### Description

Represents a list of users associated with the current organization.
Users may have different roles based on their profile.
Details about individual users, such as authentication information and full name, are provided in the specific fields of this resource.
Additionally, each user has an associated application language, which defines their preferred language within the application.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### users Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| authenticationEmail | String | Read-only | Email |
| auth0UserId | String | Read-only | Auth0 user identifier (live since 2026-06) |
| firstName | String | Read-only | User's first name (live since 2026-06) |
| lastName | String | Read-only | User's last name (live since 2026-06) |
| fullName | String | Read-only, Computed | Name and Surname. Computed field — cannot be used in filtering or sorting; order/filter on firstName and lastName instead |
| applicationLanguageCode | String | Read-only | User's language (e.g. en-US) |

**Info:**
- **applicationLanguageCode** - Allows you to know the language preference of the connected user to also localize your application.
- **auth0UserId** - Unique identifier of the user in Auth0. Can be used to reconcile the Public API user with the corresponding identity provider account.

---

## User Access Policy Check

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Action (Query) | userAccessPolicyCheck |

### Description

The UserAccessPolicyCheck service enables you to verify the current user's permissions to perform specific actions within the application, based on their roles and access rights.

Furthermore, the Sage Active solution not only considers the user's role but can also restrict access to certain data based on the Sage Active solution in use.

This service provides a mechanism to dynamically enable or disable features, or adjust the UI elements (like buttons or links), ensuring a tailored user experience that aligns with the user's permissions.

See also: Key concepts / Users management

**Functionality:**
- Permission Verification: Determines whether the current user has the rights to perform specified actions.
- Dynamic UI Adjustment: Helps in enabling or disabling UI elements based on the user's permissions.

**Required Parameters:**
- Actions: A list of action names (e.g., mutations createCustomer, deleteSalesQuote, query accountingAccounts, etc.) for which you want to check the current user's permissions.

**Response:**
- Action: The name of the action checked.
- IsAllowed: A boolean value indicating whether the user is permitted to perform the action (true) or not (false).

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### How to use the User Access Policy Check service

**GraphQL Query:**

```graphql
query userAccessPolicyCheck($actions: [String!]!) {
  userAccessPolicyCheck(actions: $actions) {
    action
    isAllowed
  }
}
```

**GraphQL Variables:**

```json
{
  "actions": ["createCustomer", "deleteSalesQuote", "accountingAccounts"]
}
```

**Example Response:**

```json
{
  "data": {
    "userAccessPolicyCheck": [
      {
        "action": "createCustomer",
        "isAllowed": true
      },
      {
        "action": "deleteSalesQuote",
        "isAllowed": false
      },
      {
        "action": "accountingAccounts",
        "isAllowed": true
      }
    ]
  }
}
```

### userAccessPolicyCheck Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| actions | Array[String] | Yes | List of action names to check permissions for |

### userAccessPolicyCheck Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| action | String | - | Name of the action checked |
| isAllowed | Boolean | - | Indicates whether the action is permitted for the current user |

### Info

For the documentPdfPreview and the documentPdfEmail operations, it is necessary to specify the document type as part of the operation name according to the type of document being accessed:
- documentPdfPreviewSalesQuote or documentPdfEmailSalesQuote for sales quotes
- documentPdfPreviewSalesOrder or documentPdfEmailSalesItemOrder for orders
- documentPdfPreviewSalesDeliveryNote or documentPdfEmailSalesDeliveryNote for delivery notes
- documentPdfPreviewSalesInvoice or documentPdfEmailSalesInvoice for invoices

The userAccessPolicyCheck action should not be included in the list of actions because this operation needs to be functional regardless of the user's profile and is not subject to access authorization.

### Error Management

If an attempt is made to check permissions for actions that are not recognized by the API, such as incorrectly named operations, this error will be returned:

```json
{
  "errors": [
    {
      "message": "global.businessErrors.unknownActionNames",
      "locations": [
        {
          "line": 2,
          "column": 3
        }
      ],
      "path": [
        "userAccessPolicyCheck"
      ],
      "extensions": {
        "details": "xustomers|xalesTariffs"
      }
    }
  ]
}
```

The message field will consistently include `global.businessErrors.unknownActionNames`, indicating that one or more actions provided do not match any known operations within the API.

The details within the extensions object provide specific insight into which actions were unrecognized.

This error can also be triggered if the action `userAccessPolicyCheck` is passed as a parameter, as this action is not subject to access control rules and should not be included in the list of actions for permission checking.

### When to Call userAccessPolicyCheck

Since it is possible to assign different roles to the same user for different organizations, userAccessPolicyCheck must be called for each organization accessible to the current user.

**Example:** John has an Admin role for organization ORG1 but has a ReadOnly role for organization ORG2. Your application utilizing the public API provides a selection interface for the list of organizations accessible to the current user. Here, John will see both ORG1 and ORG2 in the list. Your application also stores the last organization used by John in local preferences. As a result, it is necessary to call userAccessPolicyCheck and trigger the application interface to update and respect the refreshed permissions:
- At application launch after retrieving the organization stored in preferences.
- Also, each time there is a change in organization selection from the list of organizations.

### Sample Quotes App Usage

The Sample Quotes App demonstrates practical use of userAccessPolicyCheck:

1. Declaration of all queries and mutations in an object `qm` that contains a list of all objects used by the app. For each operation, the `policy` parameter contains the name of the action.

```json
{
  "queryUserProfile": {
    "context": "User Profile",
    "policy": "userProfile",
    "body": "query { userProfile { applicationLanguageCode authenticationEmail fullName } }"
  },
  "updateSalesQuote": {
    "context": "Sales Quote Update",
    "policy": "updateSalesQuote",
    "body": "mutation ($values: SalesQuoteUpdateGLDtoInput!) { updateSalesQuote (input: $values) { id } }"
  }
}
```

2. **getListOfPolicies()** - This utility function extracts all policy names from the qm object. It ensures that the list of policies is unique and free of null values.

3. **checkUserAccessPolicies()** - This function fetches the access policies applicable to the current user. It makes a GraphQL query to the userAccessPolicyCheck, passing a list of action policies. The response is processed by `createPolicyAuthorizationMap` to create a map of policies indicating which actions the user is allowed to perform.

4. **adjustButtonAccessibility(buttonId, policyAllowed, forceDisable)** - This function adjusts the accessibility of UI elements based on the user's permissions. If an action is not allowed or `forceDisable` is true, the corresponding UI element is disabled.

5. **checkUserAppAccess()** - Tests whether the user's profile permits them to use the application. For instance, if the user has a role that does not authorize them to use the Sales feature, they should be warned as soon as they log into the application.

> Note: Adjusting the interface according to user rights is important, but security is primarily ensured by the API. Even if a button's status is modified to allow execution, the API will deny unauthorized actions.

---

## Users & User Management (Role-Based Access)

The assignment of roles to users and their access to businesses (known as 'organization' in the API context) enables restriction of data access based on user roles. The public API must apply the same policies defined in these roles to align with the behavior of the Sage Active application.

Furthermore, the Sage Active solution not only considers the user's role but can also restrict access to certain data based on the Sage Active solution in use. For the same role, a user may be denied access to certain data depending on whether they are using Sage Active Starter or Sage Active Essentials.

### Examples

- **Example 1:** If a user has only the Accountant role, they cannot access features in Sage Active other than those related to accounting. Similarly, the public API will restrict access for this user to accounting data only.
- **Example 2:** If a user has only a read-only role, then the public API should not authorize mutations that allow creating, modifying, or deleting data.
- **Example 3:** If a user has the right to view businesses A and B but not C, then the public API will only allow access to organizations A and B, but not C.
- **Example 4:** If the Sage Active solution is Starter, even if a user has the admin role, they will not be able to access accounting entries.

### Role or Feature Error Management

If a query or mutation request is denied due to unauthorized rights, the API will return a message in the form:

```json
{
  "errors": [
    {
      "message": "global.businessErrors.authorizationPolicy",
      "locations": [
        {
          "line": 1,
          "column": 51
        }
      ],
      "path": [
        "createProduct"
      ],
      "extensions": {
        "details": "add-product"
      }
    }
  ],
  "data": {
    "createProduct": null
  }
}
```

The message will contain:
- `global.businessErrors.authorizationPolicy` if the user's role does not permit access to the feature.
- `global.businessErrors.authorizationFeature` if the current solution does not authorize the feature.

### Other Error Handling for Unknown Users in Sage Active

If the user is not authorized at all to use the current business, this error will be returned:

```json
{
  "errors": [
    {
      "message": "The current user is not authorized to access this resource.",
      "locations": [
        {
          "line": 2,
          "column": 3
        }
      ],
      "path": [
        "products"
      ],
      "extensions": {
        "code": "AUTH_NOT_AUTHORIZED"
      }
    }
  ],
  "data": {
    "products": null
  }
}
```

### Predicting User Permissions for Actions

From a user experience perspective, it's necessary to inform the user via the interface if an action is not authorized. For example, for a user with a read-only role, it's pointless to offer a Create, Modify, or Delete button. It's preferable to disable or hide the button rather than allowing the user to enter data only to receive a message that the action is denied.

To achieve this, the userAccessPolicyCheck action allows passing in parameters like object names (e.g., mutation createCustomer or deleteSalesQuote, query accountingAccounts), and the API will return a true or false for each object name for the current user.

---

## Countries

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | countries |

### Description

Represents a standardized list of countries, commonly used within the application.
Beyond providing the country's title and various ISO codes, this resource also furnishes information related to the VIES (VAT Information Exchange System).
VIES is a system that allows for the verification of the validity of a VAT (Value Added Tax) identification number for companies registered in the European Union.

**NEW:** If you remove the X-TenantId header, you must provide X-OrganizationId instead. One of these two headers is required to resolve the current legislation and return the correct data.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### countries Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| name | String | Read-only | Country |
| isoCodeAlpha2 | String | Read-only | ISO code 2 |
| isoCodeAlpha3 | String | Read-only | ISO code 3 |
| isoNumber | String | Read-only | ISO number |
| viesCode | String | Read-only | VIES value prefix of European Community VAT numbers |

### Info

- **isoCodeAlpha2:** Corresponds to the country code used for example for third party customers and suppliers.
- **viesCode:** Defined for all countries of the European Community, null for other countries, it relates to the prefix of the VAT numbers.
  - European Community countries except Greece and Northern Ireland: viesCode = isoCodeAlpha2
  - Greece: viesCode = EL
  - Northern Ireland: viesCode = XI
  - Other countries: viesCode = NULL

---

## Zip Codes

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | zipcodes |

### Description

Provides for a country, a standard list of zip codes with details such as province, city, and zip code, enabling accurate address identification and validation.
Zip codes are only available for countries corresponding to supported legislations.

**NEW:** If you remove the X-TenantId header, you must provide X-OrganizationId instead. One of these two headers is required to resolve the current legislation and return the correct data.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### zipcodes Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| country | String | Read-only | Country |
| province | String | Read-only | Province |
| town | String | Read-only | City |
| zipCode | String | Read-only | Zip Code |

### Filtering by country

Filtering must be performed using the country field with standardized values, which are consistent across all legislations. Use the following mapping:
- FR: "FRANCE"
- DE: "ALEMANIA"
- ES: "ESPANA"
- PT: "PORTUGAL"

FR, ES, DE and PT represent the country codes for which zip codes can be retrieved.

**Example query:**

```graphql
query ($country: String!) {
  zipCodes(
    order: [{ country: ASC }, { zipCode: ASC }]
    where: { country: { eq: $country } }
  ) {
    edges {
      node {
        id
        country
        zipCode
        province
        town
      }
    }
  }
}
```

**Example variables:**

```json
{
  "country": "FRANCE"
}
```

```json
{
  "country": "ALEMANIA"
}
```

```json
{
  "country": "PORTUGAL"
}
```

---

## Currencies

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | currencies filtered by id |
| POST | List | Query | currencies |

### Description

Represents the currency definitions used in the system.

**NEW:** If you remove the X-TenantId header, you must provide X-OrganizationId instead. One of these two headers is required to resolve the current legislation and return the correct data.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### currencies Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Unique identifier for the currency |
| creationDate | DateTime | System | The date and time when the currency was created |
| modificationDate | DateTime | System | The date and time of the last modification |
| code | String | - | ISO currency code (e.g., USD, EUR) |
| description | String | - | Description of the currency |
| precision | Int | - | The number of decimal places for the currency |

### Info

- **Code:** The official ISO 4217 currency code representing the currency.
- **Precision:** Defines the number of decimal places used in transactions involving this currency.

---

## Document Types

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | documenttypes |

### Description

Used for both customers and suppliers, this functionality determines the corresponding document type based on the document number provided.
This determination is vital for verifying the real existence of a customer or supplier, thereby ensuring their authenticity and compliance with regulations.
Depending on the legislation of Sage Active, different valid document types are listed within this section.

Globally, the API automatically handles the assignment of appropriate document types based on the legislation and the country of the clients or suppliers at the time of their creation. An exception exists for Spanish legislation ES, where it may be necessary to specify a particular document type for thirds that are not part of the VIES (VAT Information Exchange System).

**NEW:** If you remove the X-TenantId header, you must provide X-OrganizationId instead. One of these two headers is required to resolve the current legislation and return the correct data.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### documenttypes Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| code | String | Read-only | Code |
| name | String | Read-only | Name |

### Info

Allowed document types by country:

**France (FR):**

| Code | Name | Condition |
|------|------|-----------|
| 01 | SIRET | Default |
| 02 | TVA Intracommunautaire | VIES not FR |
| 06 | Autre document justificatif | Other |

**Spain (ES):**

| Code | Name | Condition |
|------|------|-----------|
| 01 | NIF/DNI | Default |
| 02 | NIF/IVA | VIES not ES |
| 03 | Pasaporte | Other |
| 04 | Documento oficial emitido en el pais de residencia | Other |
| 05 | Certificado de residencia | Other |
| 06 | Otro documento probatorio | Other |

**Germany (DE):**

| Code | Name | Condition |
|------|------|-----------|
| 01 | Steur-IdN | Default |
| 02 | USt-IdNr | VIES not DE |
| 06 | Another supporting document | Other |

### DocumentId generator for tests by legislation

FR - France, ES - Spain, DE - Germany, AT - Austria, BE - Belgium, BG - Bulgaria, CY - Cyprus, CZ - Czech Republic, DK - Denmark, EE - Estonia, EL - Greece, FI - Finland, HR - Croatia, HU - Hungary, IE - Ireland, IT - Italy, LT - Lithuania, LU - Luxembourg, LV - Latvia, MT - Malta, NL - The Netherlands, PL - Poland, PT - Portugal, RO - Romania, SE - Sweden, SI - Slovenia, SK - Slovakia, XI - Northern Ireland

---

## Unit of Measurements

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | unitOfMeasurements filtered by id |
| POST | List | Query | unitOfMeasurements |

### Description

Represents the various units of measurement known as UnitsOfMeasurements in the API context and used for defining product quantities in the product interface.
These values are standardized to ensure consistency and accuracy across all calculations.
Each unit of measurement includes metadata such as Abbreviation, Number of Decimals, and Name, providing detailed descriptions for quantities.

The Unit of measurement Sales Setup parameter allows customization of these units within the product interface to define, for each unit, whether it will be used and, if so, for which Product Categories it applies.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### unitOfMeasurements Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | - | Unique identifier of the unit of measurement |
| numberOfDecimals | Int | - | Specifies the precision for quantities |
| name | Enum | - | Values: CUBIC_METRE, DAY, GRAM, HOUR, KILOGRAM, KILOMETRE, LITRE, METRE, MILLIGRAM, MILLILITRE, MONTH, NIGHT, NOT_UNIT, PACK, PERCENTAGE, SQUARE_METRE, TONNE, UNIT |

### Info - Name Abbreviations

As the API returns enumeration values but does not return their localized translations, unitOfMeasurements does not return the abbreviations. Below are the English abbreviation values for each unit:

| Enum Value | Abbreviation |
|------------|-------------|
| NOT_UNIT | (none) |
| CUBIC_METRE | m3 |
| DAY | DAYS |
| GRAM | G |
| HOUR | HR |
| KILOGRAM | KG |
| KILOMETRE | KM |
| LITRE | L |
| METRE | M |
| MILLIGRAM | MG |
| MILLILITRE | ML |
| MONTH | MONTHS |
| NIGHT | NIGHTS |
| PACK | PACKS |
| PERCENTAGE | % |
| SQUARE_METRE | M2 |
| TONNE | T |
| UNIT | UNITS |

---

## Taxes

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | taxes |

### Description

Represents the legal taxes in effect within specific legislations (such as FR, ES, or DE).
These taxes are standard for each legislation and, therefore, cannot be modified.
Each tax is defined by its name, percentages, and an associated group, reflecting the legal requirements and tax structures within the given legislation.
The management of these standardized taxes is essential for ensuring compliance with legal tax obligations and accurately reflecting the current tax laws within the accounting and sales domain.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### taxes Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| effectiveDate | DateTime | Read-only | Effective date (date-only) |
| groupName | String | Read-only | Name group |
| group | TaxGroup | - | Fields of Tax Group (DATALOADER) |
| groupId | UUID | Read-only | Tax Group ID |
| inactive | Boolean | Read-only | Inactivation status |
| inactivationDate | DateTime | Read-only | Inactivation date (date-only) |
| name | String | Read-only | Name |
| percentage | Decimal | Read-only | Percentage |
| equivalenceSurchargePercentage | Decimal | Read-only | Surcharge percentage |
| hasEquivalenceSurcharge | Boolean | Read-only | Subject to the equivalence surcharge regime |
| taxType | Enum | Read-only | Values: NOT_SPECIFIED, DOM, FOREIGN, IGIC, IPSI, IRPF, IVA, IVA_PT, TVA, UST |

### Info

- **equivalenceSurchargePercentage / hasEquivalenceSurcharge:**
  - FR: equivalenceSurchargePercentage always 0, hasEquivalenceSurcharge always false
  - ES: The equivalence surcharge is a form of simplified VAT for retail traders who cannot recover VAT. The percentage of equivalence surcharge is applied per Spanish legislation.
  - DE: equivalenceSurchargePercentage always 0, hasEquivalenceSurcharge always false

---

## Tax Groups

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | taxGroups |

### Description

Represents a collection of related taxes grouped together for simplified tax management.
A Tax Group allows businesses to efficiently manage multiple tax rates and rules that apply to specific transactions, ensuring consistency and compliance with tax regulations.
Each tax group is associated with multiple taxes and serves as a reference for tax calculations in accounting and sales transactions.
By using tax groups, organizations can streamline tax application and reporting processes while maintaining flexibility to adapt to legislative changes.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### taxGroups Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| name | String | Read-only | Name |
| vatTaxation | Enum | Read-only | Values: NOT_SPECIFIED, NOT_SUBJECT, NOT_TAXABLE, TAXABLE. Defines whether a transaction is subject to VAT |
| taxType | Enum | Read-only | Values: NOT_SPECIFIED, DOM, FOREIGN, IGIC, IPSI, IRPF, IVA, IVA_PT, TVA, UST |
| taxGroupCode | String | Read-only | Tax Group Code |

---

## Tax Treatments

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | taxTreatments |

### Description

Used to define tax codes that associate the type of operation (such as purchasing goods, purchasing services, selling goods, etc.) with general tax accounts.
This association simplifies the entry of sales or purchase records within the accounting system.
For example, when entering a sales invoice, if the net sales account is linked to a tax code, the associated tax account will be automatically proposed.
The calculation of the net and VAT will be automatic according to the percentage of the tax.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### taxTreatments Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| description | String | Read-only | Tax Treatment description (Sales: registerType=ISSUED, Purchase: registerType=RECEIVED) |
| inactive | Boolean | Read-only | Deactivated |
| registerType | Enum | Read-only | Values: UNDEFINED, BOTH, ISSUED, RECEIVED. Taxable event |
| isIntracomunity | Boolean | Read-only | Intra-community flag |
| taxGroup | TaxGroup | - | Fields of Tax Group (DATALOADER) |
| taxGroupId | UUID | Read-only | Tax Group ID |
| taxCode | String | Read-only | Tax Code |
| taxType | Enum | Read-only | Values: NOT_SPECIFIED, DOM, FOREIGN, IGIC, IPSI, IRPF, IVA, IVA_PT, TVA, UST |
| inputAccountingAccount[] | AccountingAccount | - | Array of Input accounts |
| outputAccountingAccount[] | AccountingAccount | - | Array of Output accounts |

---

## Invoice Types

> **Source:** <https://developer.sage.com/sageactive/resources/invoicetypes>
> New query — live since the 2026-05 release.

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | invoiceTypes |

### Description

An invoice type defines the business and legal classification applied to an invoice. It is used to distinguish standard invoices, simplified invoices, and amended invoices, and provides the related register type and SII classification code when applicable.

Notably relevant for Spain: `siiCode` carries the Spanish SII classification, and the simplified-invoice type is referenced by grouped simplified invoices in accounting entries (`accountingEntryInvoice.invoiceTypeId`).

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### invoiceTypes Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| description | String | - | Invoice type description |
| isAmendInvoice | Boolean | - | Indicates whether this is an amended invoice |
| registerType | Enum | - | Values: BOTH, RECEIVED, ISSUED |
| siiCode | String | - | Spanish SII classification code (unused for the French market) |
| invoiceTypesCode | String | - | Invoice type code |

---

## Bank Accounts

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Action (Query) | bankAccounts filtered by id |

### Description

Bank Accounts represent the aggregated view of a bank in Sage Active. They combine:
- the definition of the bank account stored in Sage Active (payment method configuration),
- and the banking information exposed by the bank, such as balances, synchronization status, and transaction availability.

A Bank Account exposes both accounting-related data and banking synchronization data through a single object.

This operation is an action, not a classic query supporting pagination, filtering, or sorting. It returns a static list of all banks declared and available in Sage Active. The result set is predefined and does not depend on dynamic search criteria or runtime parameters.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### bankAccounts Fields

**Identification:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Identifier of the payment method |
| creationDate | DateTime | System | Creation date |
| modificationDate | DateTime | System | Last modification date |
| disabled | Boolean | - | Indicates whether the payment method is disabled |
| referenceName | String | - | Reference name (user-defined) |
| type | Enum | - | Values: NOT_SPECIFIED, BANK_ACCOUNT, CASH. Payment method type |
| iban | String | - | IBAN |
| bic | String | - | BIC / SWIFT |
| bankName | String | - | Bank name (from banking aggregation service) |
| bankingBankLogo | String | - | Bank logo URL (for UI display) |

**Accounting data:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| accountingAccountCode | String | - | Accounting account code |
| accountingAccountTotalBalance | Decimal | - | Total balance of the accounting account |
| subAccountId | UUID | - | Linked accounting sub-account |
| journalTypeId | UUID | - | Journal type identifier |

**Banking balances:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| balanceAmount | Decimal | - | Current balance |
| bankingAvailableBalanceAmount | Decimal | - | Available balance amount |
| bankingLedgerBalanceAmount | Decimal | - | Ledger balance amount |
| bankingAvailableBalanceDate | DateTime | - | Available balance date (date-only) |
| bankingLedgerBalanceDate | DateTime | - | Ledger balance date (date-only) |

**Connection & synchronization:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| bankingLastSyncDate | DateTime | - | Last banking synchronization date (date-only) |
| bankingPendingTransactions | Int | - | Number of pending banking transactions |
| bankingHasTransactions | Boolean | - | Indicates if transactions exist |
| bankingLastTransactionsReceived | DateTime | - | Last time transactions were received (date-only) |

### Info

**Bank account identification:**
- **referenceName:** User-defined name of the bank account in Sage Active. Used for display and identification purposes only.
- **type:** Payment method type associated with the bank account.
- **bankName:** Name of the synchronized bank as provided by the banking aggregation service. May differ from the user-defined reference name.
- **bankingBankLogo:** URL pointing to the bank logo provided by the banking aggregation service. Informational and intended for UI display purposes only.

**Banking balances:**
- **balanceAmount:** Current balance of the bank account as exposed in Sage Active. May come from the last synchronization and can differ from real-time bank balance.
- **bankingAvailableBalanceAmount:** Available balance reported by the bank, taking into account pending operations such as card payments or authorizations.
- **bankingLedgerBalanceAmount:** Ledger balance provided by the bank, corresponding to the balance after booked transactions only, excluding pending ones.
- **bankingAvailableBalanceDate:** Date at which the available balance was calculated by the bank.
- **bankingLedgerBalanceDate:** Date at which the ledger balance was calculated by the bank.

**Connection & synchronization:**
- **bankingLastSyncDate:** Date and time of the last successful synchronization with the bank.
- **bankingPendingTransactions:** Number of transactions retrieved from the bank but not yet fully processed or accounted for.
- **bankingHasTransactions:** Indicates whether at least one banking transaction has been retrieved for this bank account. Does not imply transactions are posted in accounting.
- **bankingLastTransactionsReceived:** Date and time when the last batch of banking transactions was received from the bank.

---

## Banks Overview

This section describes how banks are managed in Sage Active. It explains the relationship between Bank Accounts and Payment Methods, and how banking data and accounting configuration are combined.

### Resources

**Bank Accounts** represent the aggregated view of a bank in Sage Active. They combine:
- the definition of the bank account stored in Sage Active (payment method configuration),
- and the banking information exposed by the bank, such as balances, synchronization status, and transaction availability.

**Payment Methods** represent the configuration of a bank account inside Sage Active. They define how the bank is used in business operations, such as sales, purchases, and payments, but do not expose banking synchronization data. Payment Methods contain only the data related to: accounting setup, payment usage, and functional configuration within Sage Active.

### Relationship between Bank Accounts and Payment Methods

- A Bank Account is built on top of a Payment Method.
- The id of a Bank Account is the same identifier as the underlying Payment Method.
- Bank Accounts enrich Payment Methods with banking data retrieved from the bank, such as balances and synchronization status.
- In other words: Payment Methods expose how a bank account is defined and used in Sage Active. Bank Accounts expose the same entity, augmented with live or synchronized banking information.

---

## Bank Movements

> **Source:** <https://developer.sage.com/sageactive/resources/bankmovements>
> Added in 2026-04 release.

### HTTP Operations

| HTTP | Operation | Type | Object |
|------|-----------|------|--------|
| POST | Read | Query | `bankMovements` filtered by id |
| POST | List | Query | `bankMovements` |

### Description

Bank movements are operations imported from the bank, used for reconciliation with accounting. Each record combines data returned by the banking connection (amount, dates, reference, labels) with Sage Active processing state.

**Scope:** the list only includes movements for bank accounts that are actually connected to their financial institution. Accounts without a live banking connection do not supply rows.

**How to query:** restrict the list to a single bank account by passing a filter on `bankAccountId`. Only movements whose `bankAccountId` matches the given id are returned.

### GraphQL Query Example

```graphql
query {
  bankMovements(first: 50, where: { bankAccountId: { eq: "2c6e8a4e-0d1a-4f0b-9a3b-1e2d3c4b5a60" } }) {
    edges {
      node {
        id
        bankAccountId
        linkStatus
        linkStatusDate
        dateFundsAvailable
        datePosted
        narrative1
        referenceNumber
        transactionAmount
        transactionNarrative
        transactionStatus
        transactionType
        linkedAccountingEntries { id accountingEntryId bankJournalTypeId date description documentNumber isClosed number status }
        proposalItems { id bankAccountingEntryId bankBillOfExchangeId bankingRuleId itemAmount ponderationScore }
        reconciledItems { id bankAccountingEntryId bankBillOfExchangeId bankingRuleId bankingRuleRejected linked originProposalItemId reconciledBy suggestedBy }
      }
    }
  }
}
```

### bankMovements Fields

| Field | Type | Description |
|-------|------|-------------|
| id | UUID (system) | Unique identifier of the bank transaction |
| bankAccountId | UUID | Bank account (payment method) the movement belongs to |
| transactionAmount | Decimal | Transaction amount as reported by the bank |
| datePosted | DateTime (date-only) | Value date or posting date from the bank |
| dateFundsAvailable | DateTime (date-only) | Date from which funds are available (when provided by the bank) |
| transactionNarrative | String | Main narrative or label for the operation |
| narrative1 | String | Additional narrative from the bank |
| referenceNumber | String | Bank reference (e.g. end-to-end or operation reference) |
| transactionType | String | Type of operation as provided by the bank (provider-specific) |
| transactionStatus | String | Status of the operation on the bank side (provider-specific) |
| linkStatus | Enum | NOT_SPECIFIED, DISMISSED, IN_PROGRESS, LINKED, LINKED_WITH_VARIANCE, PENDING — state of the link between this movement and accounting |
| linkStatusDate | DateTime (date-only) | When the current link status was last set |
| linkedAccountingEntries[] | Array | Bank journal entries associated with this movement |
| proposalItems[] | Array | Suggested matches (e.g. rules) pending validation |
| reconciledItems[] | Array | Reconciled or rejected links between this movement and accounting or other documents |

### bankMovements / linkedAccountingEntries

| Field | Type | Description |
|-------|------|-------------|
| id | UUID (system) | Identifier of the bank accounting entry line |
| accountingEntryId | UUID | Related accounting entry |
| bankJournalTypeId | UUID | Bank journal type |
| date | DateTime (date-only) | Entry date |
| number | Int | Line or sequence number |
| documentNumber | String | Document number |
| description | String | Description |
| status | String | Status of the bank accounting entry |
| isClosed | Boolean | Whether the entry is closed |

### bankMovements / proposalItems

| Field | Type | Description |
|-------|------|-------------|
| id | UUID (system) | Identifier of the proposal item |
| itemAmount | Decimal | Proposed amount for this match |
| ponderationScore | Int | Internal score for ranking proposals |
| bankAccountingEntryId | UUID | Target bank accounting entry, if any |
| bankBillOfExchangeId | UUID | Target bill of exchange, if any |
| bankingRule | BankingRule (DATALOADER) | Fields of BankingRule |
| bankingRuleId | UUID | Id of BankingRule that generated this proposal |

### bankMovements / reconciledItems

| Field | Type | Description |
|-------|------|-------------|
| id | UUID (system) | Identifier of the reconciled item |
| linked | Boolean | Whether the item is currently linked |
| bankingRuleRejected | Boolean | Whether a suggested banking rule was rejected |
| bankingRule | BankingRule (DATALOADER) | Fields of BankingRule |
| bankingRuleId | UUID | Id of related BankingRule, if any |
| bankAccountingEntryId | UUID | Resulting or related accounting entry, if any |
| bankBillOfExchangeId | UUID | Resulting or related bill of exchange |
| originProposalItemId | UUID | Source proposal item when the reconciliation was confirmed from a proposal |
| reconciledBy | String | Who or what applied the reconciliation (product-specific) |
| suggestedBy | String | Who or what suggested the link (product-specific) |

### Info

- `linkStatus` indicates how far the movement is in the matching flow with accounting (e.g. not yet processed, in progress, linked). Use `localizedEnumValues` for translations.
- `proposalItems` represent candidates that Sage Active suggests before you confirm reconciliation (often produced by banking rules).
- `reconciledItems` represent outcomes (confirmed link, rejected banking rule, etc.), including optional references to an origin proposal.
- `linkedAccountingEntries` are the bank-journal entries tied to this movement — not a replacement for the full `accountingEntries` list API.

---

## Banking Rules

> **Source:** <https://developer.sage.com/sageactive/resources/bankingrules>
> Added in 2026-04 release.

### HTTP Operations

| HTTP | Operation | Type | Object |
|------|-----------|------|--------|
| POST | Read | Query | `bankingRules` filtered by id |
| POST | List | Query | `bankingRules` |

### Description

Banking rules drive how bank movements are matched, suggested, or posted to the chart of accounts and related documents. A rule can target a bank account, an accounting account, third parties, and other criteria; it carries scoring, status, and optional extended metadata.

`bankingRuleId` is referenced from `bankMovements.proposalItems` and `bankMovements.reconciledItems` to identify which rule produced or affected a match.

### bankingRules Fields

**Identity:**

| Field | Type | Description |
|-------|------|-------------|
| id | UUID (system) | Unique identifier of the banking rule |
| creationDate | DateTime (system) | Creation time |
| modificationDate | DateTime (system) | Last modification time |

**Targets:**

| Field | Type | Description |
|-------|------|-------------|
| accountingAccountId | UUID | Default or target accounting account, when the rule uses one |
| bankAccountId | UUID | Bank account the rule applies to, when set |
| thirdPartyId | UUID | Linked third party, when the rule is scoped to one |
| categoryId | UUID | Transaction category for classification, when set |

**Configuration:**

| Field | Type | Description |
|-------|------|-------------|
| action | Enum | NOT_SPECIFIED, CREATE_NON_INVOICE, CREATE_PURCHASE_INVOICE, CREATE_SALES_INVOICE, ONLY_MATCH — what the rule does when it matches |
| kind | Enum | NOT_SPECIFIED, ALL_BANK_ACCOUNTS, ONE_BANK_ACCOUNT — whether the rule applies to one bank account, all, or is unspecified |
| name | String | Name of the rule |
| status | String | Status of the rule in the product |
| defaultProposalRule | Boolean | When true, the rule is used for default proposal behaviour |
| ponderationScore | Int | Base score used for proposal ranking (see `proposalItems` on bank movements) |
| ruleApplied | Int | How the rule is applied (internal code) |
| sourceType | Enum | ACCOUNTING, AP_AUTOMATION, BANK_SMART_AGENT, BANKING, E_INVOICING_AP_AUTOMATION, EMAIL_AP_AUTOMATION, FIXED_ASSETS, GLOBAL, IMPORT_FILES, INTEGRATION_API, ONLINE_PAYMENTS, PUBLIC_API, PUBLIC_API_AP_AUTOMATION, SAGE_DE_PAYROLL_CLOUD, SAGE_FR_PAYROLL_CLOUD, SAGE_MOBILE_APP_AP_AUTOMATION, SALES, SCHEDULER_SERVICE, YEAR_END_PROCESS, YEAR_END_PROCESS_PREVIEW — where the rule originates from |
| suggestionsProvided | Int | Number of suggestions associated with the rule (default 0) |
| createdByDefault | Boolean | Whether the record was created by the system as a default |
| disabled | Boolean | When true, the rule is disabled |
| disabledDate | DateTime (date-only) | Date the rule was disabled, when set |
| description | String | User-visible description |
| rationale | String | Rationale or comment for the rule |
| createdBy | String | User or system that created the record |
| updatedBy | String | User that last updated the record |

---

## Reconcile Bank Movement

> **Source:** <https://developer.sage.com/sageactive/resources/reconcilebankmovement>
> Added in 2026-04 release. Action (mutation, returns HTTP 200).

### HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Other | ⚙️ Action (Mutation) | `reconcileBankMovement` | `ReconcileBankMovementGLDtoInput` |

### GraphQL Mutation

```graphql
mutation ($input: ReconcileBankMovementGLDtoInput!) {
  reconcileBankMovement(input: $input) {
    id
  }
}
```

**Variables:**

```json
{
  "input": {
    "bankTransactionId": "11111111-1111-1111-1111-111111111111",
    "bankingRuleId": null,
    "accountingEntries": [
      { "accountingEntryId": "22222222-2222-2222-2222-222222222222" },
      { "accountingEntryId": "33333333-3333-3333-3333-333333333333" }
    ]
  }
}
```

**Example response:**

```json
{ "data": { "reconcileBankMovement": { "id": "11111111-1111-1111-1111-111111111111" } } }
```

### Input Fields — `ReconcileBankMovementGLDtoInput`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| bankTransactionId | UUID | Yes | Bank movement to reconcile (same entity as the `id` of the bank movement list) |
| bankingRuleId | UUID | No | Optional. Rule to attach (e.g. one taken from the movement's `proposalItems`) |
| accountingEntries[] | Array | Yes | At least one accounting line to link to the movement; each item references an accounting entry |

**accountingEntries[] item:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| accountingEntryId | UUID | Yes | Accounting entry to reconcile with the bank movement |

### Returned Type

| Field | Type | Description |
|-------|------|-------------|
| id | UUID | Identifier of the resource returned by the mutation (typically the bank transaction / movement) |

---

## Unreconcile Bank Movement

> **Source:** <https://developer.sage.com/sageactive/resources/unreconcilebankmovement>
> Live since the 2026-05 release.

### HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Other | ⚙️ Action (Mutation) | `unReconcileBankMovement` | `UnReconcileBankMovementGLDtoInput` |

> Note: the mutation name uses a capital R (`unReconcileBankMovement`).

### GraphQL Mutation

```graphql
mutation ($input: UnReconcileBankMovementGLDtoInput!) {
  unReconcileBankMovement(input: $input) {
    id
  }
}
```

**Variables:**

```json
{
  "input": {
    "bankTransactionId": "11111111-1111-1111-1111-111111111111"
  }
}
```

**Example response:**

```json
{ "data": { "unReconcileBankMovement": { "id": "11111111-1111-1111-1111-111111111111" } } }
```

### Input Fields — `UnReconcileBankMovementGLDtoInput`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| bankTransactionId | UUID | Yes | Bank movement to unreconcile (same entity as the `id` of the bank movement list) |

### Returned Type

| Field | Type | Description |
|-------|------|-------------|
| id | UUID | Identifier of the resource returned by the mutation (typically the bank transaction / movement) |

---

## Payment Means

### HTTP Operations

| Method | Operation | Type | Object | DTO |
|--------|-----------|------|--------|-----|
| POST | Create | Mutation | createPaymentMean | PaymentMeanCreateGLDtoInput |
| POST | Delete | Mutation | deletePaymentMean | PaymentMeanDeleteGLDtoInput |
| POST | Read | Query | paymentMeans filtered by id | |
| POST | List | Query | paymentMeans | |

### Description

Represents the various means of payment used by payment terms within an organization, known as PaymentMean in the API context and Payment Method in the product interface.
Examples include Cash, Check, Bank Transfer, Direct Debit, and more.
These payment means are customizable and local to each organization, allowing for flexibility in defining the ways payments can be received or made.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### paymentMeans Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| description | String(20) | *Required | Name (must be unique) |
| type | Enum | Read-only, NEW | Values: UNDEFINED, PAYMENT_IN. Indicates the payment mean type. Default: UNDEFINED |
| inactive | Boolean | Read-only, NEW | Indicates whether the payment mean is inactive. Default: false |

### Info

- **description:** must be unique.
- **type:**
  - UNDEFINED - Default value when no specific behavior is defined.
  - PAYMENT_IN - Indicates that the payment mean corresponds to an online payment process.

---

## Payment Methods

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | paymentMethods filtered by id |
| POST | List | Query | paymentMethods |

### Description

Represents the various Banks accounts known as PaymentMethod in the API context and Bank accounts and cash in the product interface.
Each element is associated with a Journal Type and a bank account.
The payment methods are notably used by the Open Items Settlement action.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### paymentMethods Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| referenceName | String | - | Reference name |
| type | Enum | - | Values: NOT_SPECIFIED, BANK_ACCOUNT, CASH |
| disabled | Boolean | - | If the payment method is disabled |
| journalType | JournalType | - | Fields of JournalType (DATALOADER) |
| journalTypeId | UUID | - | JournalType Id |
| subAccount | AccountingAccount | - | Fields of AccountingAccount (DATALOADER) |
| subAccountId | UUID | - | Id of accountingAccount |

---

## Payment Terms

### HTTP Operations

| Method | Operation | Type | Object | DTO |
|--------|-----------|------|--------|-----|
| POST | Create | Mutation | createPaymentTerm | PaymentTermCreateGLDtoInput |
| POST | Delete | Mutation | deletePaymentTerm | PaymentTermDeleteGLDtoInput |
| POST | Read | Query | paymentTerms filtered by id | |
| POST | List | Query | paymentTerms | |

### Description

Used to define different templates of list of payment terms for a customer or supplier.

Example of a template:
- Advance payment by check 30%
- 30% by transfer at 30 days net
- the balance of 40% by bank transfer at 60 days end of month

Within the PaymentTerm, the specific conditions of payment are detailed under paymentTerms/lines.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### paymentTerms Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| name | String(50) | *Required | Name |
| lines[] | Array | - | List of all Payment terms |

### paymentTerms/lines Fields

Payment terms, used to define the list of payment terms for PaymentTerm.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| order | Int | *Required | Classification of lines from 0 to n. If REMAINING_AMOUNT, must always be last with the value 9999. |
| paymentMean | PaymentMean | - | Fields of PaymentMean (DATALOADER) |
| paymentMeanId | UUID | - | Id of the payment mean |
| type | Enum | - | Values: NONE, FIXED_AMOUNT, PERCENTAGE, REMAINING_AMOUNT. Type of calculation for the payment term line |
| value | Decimal | *Required | Numerical value used based on the type of calculation |
| condition | Enum | - | Values: NONE, DAY_OF_PAYMENT, END_OF_MONTH. To calculate due date |
| day | Int | - | Number of days |
| payDays[] | Int | - | Array of optional due days |

### Info

- **order:** If REMAINING_AMOUNT, must always be last with the value 9999.
- **payDays:** Array of optional due days. The day contained in the array closest to the number of days defined in day will be used to calculate the Due Date.
- **type:** Defines the method used for payment term calculations:
  - NONE: No specific calculation is applied.
  - PERCENTAGE: value represents a percentage of the total amount.
  - FIXED_AMOUNT: value is a fixed monetary amount.
  - REMAINING_AMOUNT: The remaining balance after all prior calculations. Must always be last in the order.
- **value:** Represents the numerical input for the calculation based on type.
  - If type = PERCENTAGE, value should be between 0 and 100.
  - If type = FIXED_AMOUNT, value is a fixed currency amount (e.g., 500).
  - If type = REMAINING_AMOUNT, value is ignored; the system automatically calculates the remaining balance.
- **Examples:**
  - If type = PERCENTAGE and value = 30, the payment term will cover 30% of the total amount.
  - If type = FIXED_AMOUNT and value = 500, the payment term will apply a fixed amount of 500.
  - If type = REMAINING_AMOUNT, the system will allocate the remaining balance. In this case, order must always be set to 9999.

---

## Business Areas

### HTTP Operations

| Method | Operation | Type | Object | DTO |
|--------|-----------|------|--------|-----|
| POST | Create | Mutation | createBusinessArea | BusinessAreaCreateGLDtoInput |
| POST | Delete | Mutation | deleteBusinessArea | BusinessAreaDeleteGLDtoInput |
| POST | Read | Query | businessAreas filtered by id | |
| POST | List | Query | businessAreas | |

### Description

Defines the specific categories of customers or suppliers within an organization, such as Trading or Services.
It can also be used to indicate the legal status.
Known as BusinessArea in the API context and Category in the product interface.
These business areas are customizable for each organization and are relevant only in the context of third-party customers and suppliers.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### businessAreas Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| name | String(17) | *Required | Category |

---

## Job Areas

### HTTP Operations

| Method | Operation | Type | Object | DTO |
|--------|-----------|------|--------|-----|
| POST | Create | Mutation | createJobArea | JobAreaCreateGLDtoInput |
| POST | Read | Query | jobAreas filtered by id | |
| POST | List | Query | jobAreas | |

### Description

Refers to the specific departments or areas where a contact within an organization works. Examples include Administration, Warehouses, Purchases, Accounting, Direction, Finance, Research and Development, Logistics, and more.
Known as JobArea in the API context and Contact information / Area in the product interface.
These job areas are customizable for each organization and are relevant only in the context of contacts.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### jobAreas Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| name | String(50) | *Required | Name of job area |

---

## Dimensions

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | dimensions |

### Description

Configuration of analytic categories, known as dimensions and tags in the API context, and displayed as Categories and Tags in the product interface.
The Dimensions resource allows you to retrieve all available dimensions and their associated tags.

> **Warning:** Analytic tag settings are currently read-only. At this stage, creating or modifying these settings is not supported via the API and must be done directly from the Settings interface in Sage Active.

An analytic dimension represents a customizable axis used to categorize accounting entry lines for analytical purposes. Typical examples include dimensions such as Warehouse, Department, or Project.

Each dimension is configured with a list of tags, which define the allowed values for that dimension. For example, the Warehouse dimension may include tags such as Lyon, Toulouse, and Rouen, each representing a specific location used in allocation.

When creating accounting entries, you can allocate each line across one or more analytic dimensions by assigning a specific tag from the list defined for each dimension.

You can also configure accounts in the chart of accounts to assign default dimension tags, which will be automatically applied to entry lines using those accounts.

### How to list dimensions and their tags

**GraphQL Query:**

```graphql
query {
  dimensions {
    edges {
      node {
        id
        code
        name
        description
        isActive
        creationDate
        modificationDate
        dimensionTags {
          id
          tagCode
          name
          isActive
        }
      }
    }
  }
}
```

**Example Response:**

```json
{
  "data": {
    "dimensions": {
      "edges": [
        {
          "node": {
            "id": "f501e055-1b21-4adf-89e6-9d2e00dfe001",
            "code": "WAREHOUSE",
            "name": "Warehouse",
            "description": "Main warehouses in use",
            "isActive": true,
            "creationDate": "2025-06-01T10:23:00Z",
            "modificationDate": "2025-06-05T08:12:30Z",
            "dimensionTags": [
              {
                "id": "99ae4c65-4cc7-4767-9424-084ad13f2498",
                "tagCode": "TOULOUSE",
                "name": "Toulouse",
                "isActive": true
              },
              {
                "id": "73b0fbc1-1d10-4a4d-89b7-44e91c6a7fa1",
                "tagCode": "LYON",
                "name": "Lyon",
                "isActive": true
              }
            ]
          }
        }
      ]
    }
  }
}
```

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### dimensions Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| code | String(20) | *Required | Code of the dimension (unique) |
| name | String(50) | *Required | Display name |
| description | String(255) | *Required | Optional description |
| isActive | Boolean | *Required | Whether the dimension is active |
| dimensionTags[] | Array | - | Associated analytic tags |

### dimensions/dimensionTags Fields

This section describes the analytic tags associated with a dimension. Tags define the actual values that can be used for allocation on that analytic axis.

> **Warning:** Dataloaders are not currently available for dimensions. This means that if you need to retrieve additional details (e.g., dimension name for a given dimensionId), you must manually correlate the values using the identifiers (id fields) returned by the API.

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| dimensionId | UUID | - | Id of the dimension (e.g. Warehouse) |
| tagCode | String(20) | *Required | Code of the tag (unique) |
| name | String(50) | *Required | Display name |
| isActive | Boolean | *Required | Whether the tag is active |

### Info

- **tagCode:** Unique identifier (code) of the tag within the dimension.
- **name:** Display name of the tag shown in UI and documents.
- **isActive:** Indicates if the tag is available for use in allocations.

---

## Transaction Categories

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | List | Query | transactioncategories |

### Description

Configuration of accounting transaction categories used to classify accounting operations.
The Transaction Categories resource allows you to retrieve all available categories and their settings.

- Set up to classify purchase and expense transactions linked to suppliers.
- Set up to classify bank transactions not linked to customers or suppliers.
- Set up to record adjustment transactions used during bank reconciliation, such as currency exchange differences or miscellaneous accounting adjustments.

### How to retrieve purchase transaction categories

**GraphQL Query:**

```graphql
query {
  transactionCategories(
    where: {
      type: { eq: PURCHASE }
    }
  ) {
    edges {
      node {
        id
        reason
        accountingAccountId
        accountingAccount {code name}
        taxTreatmentId
        taxTreatment {taxCode}
        type
        createdByDefault
        disabled
        default
      }
    }
  }
}
```

**Example Response (type = PURCHASE):**

```json
{
  "data": {
    "transactionCategories": {
      "edges": [
        {
          "node": {
            "id": "02d964fa-24f5-4953-81e1-a974fa572328",
            "reason": "Entretien et reparations des locaux",
            "accountingAccountId": "51d78706-4721-4f2f-a257-3b8c2dc7ee0b",
            "accountingAccount": {
              "code": "615000",
              "name": "Entretien et reparations"
            },
            "taxTreatmentId": "40904303-5c02-4784-a505-9c0325b7a40f",
            "taxTreatment": {
              "taxCode": "C0005"
            },
            "type": "PURCHASE",
            "createdByDefault": true,
            "disabled": false,
            "default": false
          }
        },
        {
          "node": {
            "id": "045c83ad-f992-4f52-b71c-9994e2ce250a",
            "reason": "Audit",
            "accountingAccountId": "51d78706-46dd-4f2f-a257-3b8c2dc7ee0b",
            "accountingAccount": {
              "code": "622600",
              "name": "Honoraires"
            },
            "taxTreatmentId": "40904303-5c02-4784-a505-9c0325b7a40f",
            "taxTreatment": {
              "taxCode": "C0005"
            },
            "type": "PURCHASE",
            "createdByDefault": true,
            "disabled": false,
            "default": false
          }
        },
        {
          "node": {
            "id": "05101b46-5ca7-4867-ac40-e31350926226",
            "reason": "Achats intracommunautaires",
            "accountingAccountId": "51d78706-4999-4f2f-a257-3b8c2dc7ee0b",
            "accountingAccount": {
              "code": "601090",
              "name": "Achats intracommunautaires"
            },
            "taxTreatmentId": "80b4ba02-3459-4472-9233-5dc778efebe7",
            "taxTreatment": {
              "taxCode": "C0036"
            },
            "type": "PURCHASE",
            "createdByDefault": true,
            "disabled": false,
            "default": false
          }
        }
      ]
    }
  }
}
```

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### transactioncategories Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| reason | String | *Required | Optional reason associated with the category |
| type | Enum | *Required | Values: NOT_SPECIFIED, ADJUSTMENTS, NON_INVOICE, PURCHASE |
| default | Boolean | *Required | Indicates if the category is the default one |
| createdByDefault | Boolean | *Required | Indicates if the category was system-generated |
| disabled | Boolean | *Required | Indicates if the category is disabled |
| disabledDate | DateTime | *Required | Date when the category was disabled |
| accountingAccount | AccountingAccount | - | Fields of AccountingAccount (DATALOADER) |
| accountingAccountId | UUID | - | Id of accountingAccount |
| taxTreatment | TaxTreatment | - | Fields of TaxTreatment (DATALOADER) |
| taxTreatmentId | UUID | - | Id of taxTreatment |

### Info

- **type:** Category classification used to drive business rules and UI grouping.
  - PURCHASE - Used to classify purchase and expense transactions linked to suppliers.
  - NON_INVOICE - Used to classify bank transactions not linked to customers or suppliers.
  - ADJUSTMENTS - Used to record adjustment transactions during bank reconciliation, such as currency exchange differences or miscellaneous accounting adjustments.

---

## Accounting Exercises

### HTTP Operations

| Method | Operation | Type | Object | DTO |
|--------|-----------|------|--------|-----|
| POST | Create | Mutation | createAccountingExercise | AccountingExerciseCreateGLDtoInput |
| POST | Read | Query | accountingExercises filtered by id | |
| POST | List | Query | accountingExercises | |

### Description

Corresponds to the fiscal years within the field of accounting, known as AccountingExercise in the API context and Fiscal years in the product interface.
A fiscal year, or accounting period, is a specific timeframe used by governments and businesses for accounting purposes to compare financial results and statements.

An exercise can be in one of two states:
- **Open:** During an open exercise, entries can be made or modified, reflecting the ongoing transactions and adjustments within the given fiscal year.
- **Closed:** Once an exercise is closed, the entries are finalized, and no further modifications to the accounting records for that fiscal year are permitted.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### accountingExercises Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| exercise | Int | *Required, Immutable | Year exercise |
| description | String(100) | *Required, Immutable | Name exercise |
| startDate | DateTime | *Required | Start date (date-only) |
| endDate | DateTime | - | End date (date-only) |
| numberPeriods | Int | *Required, Immutable | Number months |
| status | Enum | - | Values: UNDEFINED, CLOSED, OPEN |
| periods[] | Array | - | Start and End date periods |

### accountingExercises/periods Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| startDate | DateTime | - | Start date (date-only) |
| endDate | DateTime | - | End date (date-only) |
| month | Enum | - | Values: APRIL, AUGUST, DECEMBER, FEBRUARY, JANUARY, JULY, JUNE, MARCH, MAY, NOVEMBER, OCTOBER, SEPTEMBER |
| isClosed | Boolean | - | If true = closed |

---

## Accounting Journal Types

### HTTP Operations

| Method | Operation | Type | Object | DTO |
|--------|-----------|------|--------|-----|
| POST | Create | Mutation | createJournalType | JournalTypeCreateGLDtoInput |
| POST | Update | Mutation | updateJournalType | JournalTypeUpdateGLDtoInput (NEW) |
| POST | Delete | Mutation | deleteJournalType | JournalTypeDeleteGLDtoInput |
| POST | Read | Query | journalTypes filtered by id | |
| POST | List | Query | journalTypes | |

### Description

Define the various journals used during the entry of accounting documents, known as JournalType in the API context and Journal in the product interface.
It's important to note that these definitions refer to the definition of the journals themselves and not to the grouped entries that are found in the accountingEntry.

Each journal is associated with a specific type:
- **sales:** Used for entering customer sales accounting documents, providing access to deadlines and taxes.
- **purchases:** Used for entering supplier purchase accounting documents, providing access to deadlines and taxes.
- **financial:** Used for entering customer/supplier payments and other banking operations.
- **general:** Used for entering miscellaneous accounting documents.
- **carry forward:** Used at the close of the fiscal year to generate carry forward entries.

**Update:** Only the `deactivated` and `name` fields can be updated. In addition, business rules may prevent a journal from being deactivated.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### journalTypes Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| code | String(20) | *Required, Immutable | Code |
| name | String(50) | *Required | Name |
| deactivated | Boolean | *Required | Deactivated (journal not available for new entries, still shown in existing info) |
| type | Enum | *Required, Immutable | Values: CARRY_FORWARD, CLOSING, FINANCIAL, GENERAL, PURCHASE_INVOICE, SALES_INVOICE |
| isCreditNote | Boolean | Immutable | Indicates whether the document is a credit note (ES only) |
| creditNoteReason | Enum | Immutable | Values: UNDEFINED, R1, R2, R3, R4, R5. Reason for issuing the credit note (ES only) |
| accountingAccount | AccountingAccount | - | Fields of AccountingAccount (DATALOADER) |
| accountingAccountId | UUID | Immutable | Account (used for type Financial, Carry forward and General) |

### Info

- **accountingAccountId:** Used to define an account for type Financial, Carry forward and General.
- **deactivated:** Deactivating a Journal means that the Journal is not available for generating new accounting entries. But it will still be displayed in all the information that has been generated using that Journal.
- **type:**
  - Financial: Customer/supplier payments and other banking operations.
  - Carry forward: Close of the fiscal year to generate carry forward entries for fiscal year +1.
  - General: Miscellaneous accounting documents.
  - Purchases: Supplier purchase accounting documents.
  - Sales: Sales accounting documents.
  - Closing: Close of the current fiscal year (used for ES; unused for FR and DE).
- **isCreditNote / creditNoteReason:** Only for Spanish legislation. For Spanish legislation, and only if the journal type is PURCHASE_INVOICE or SALES_INVOICE, it is possible to specify that the journal is used for credit notes. In this case, it is mandatory to specify a reason:
  - R1: Return of packaging
  - R2: Bankruptcy proceedings
  - R3: Bad debt
  - R4: Other corrective invoices
  - R5: Simplified invoice correction

---

## Third Party Code Mode Settings

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Action (Query) | thirdPartyCodeModeSettings |

### Description

The thirdPartyCodeModeSettings service allows you to determine how third-party codes are managed for the current organization.
For each third-party type (Customer, Supplier, Employee), this service indicates whether the code must be entered manually by the user or is automatically generated by the system.

This information is primarily intended for user interface logic, enabling applications to dynamically adapt the behavior of the Code field during third-party creation or edition.

Typical use cases include:
- displaying the Code field as editable or read-only
- enforcing mandatory input when manual mode is enabled
- hiding or disabling the field when the code is auto-generated

**Functionality:**
- Indicates whether Customer, Supplier, and Employee codes are manual or automatic
- Enables UI-driven behavior without exposing internal numbering rules
- Ensures consistent third-party creation across applications

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### How to use the Third Party Code Mode Settings service

**GraphQL Query:**

```graphql
query {
  thirdPartyCodeModeSettings {
    customerCodeIsManual
    supplierCodeIsManual
    employeeCodeIsManual
  }
}
```

**Example Response:**

```json
{
  "data": {
    "thirdPartyCodeModeSettings": {
      "customerCodeIsManual": false,
      "supplierCodeIsManual": true,
      "employeeCodeIsManual": true
    }
  }
}
```

### thirdPartyCodeModeSettings Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| customerCodeIsManual | Boolean | - | Indicates whether the customer code must be manually entered |
| supplierCodeIsManual | Boolean | - | Indicates whether the supplier code must be manually entered |
| employeeCodeIsManual | Boolean | - | Indicates whether the employee code must be manually entered |

### Info

- When a value is set to **true**, the corresponding Code field must be provided by the user and should be displayed as editable and mandatory.
- When a value is set to **false**, the Code is automatically generated by the system and the field should be read-only or hidden in the user interface.
- This service does not expose numbering rules or sequences. It only provides behavioral guidance for UI rendering.

### When to Call thirdPartyCodeModeSettings

This service should typically be called:
- when initializing a third-party creation or edition form
- when switching organizations
- when dynamically adjusting form behavior based on organization settings

Because these settings are organization-specific, the service must be executed within the context of the currently selected organization.

**Example:** If the organization uses automatic numbering for customers but manual codes for suppliers, the Customer Code field should be hidden or disabled, and the Supplier Code field should be displayed and required. This avoids invalid submissions and ensures a consistent user experience aligned with organization configuration.

---

## Third Party Identification (Sirene API)

### HTTP Operations

| Method | Operation | Type | Object | DTO |
|--------|-----------|------|--------|-----|
| POST | Read | Action (Query) | thirdPartyIdentification | thirdPartyIdentificationGLDtoInput |

### Description

The Sirene API allows querying the Sirene register of companies and establishments, managed by INSEE.
It relies on the official French public registry SIRENE and therefore returns French companies only.

- The service uses the French public API Sirene open data.
- Returns identification data for French companies only.
- Useful when creating customers or suppliers, to help find a company and retrieve identification data, including the address.
- Can also be used to retrieve establishments by business activity (APE / NAF), city, or street, for prospecting and qualification purposes.
- Supports main and advanced filtering.
- Allows limiting the number of returned records.

This operation is an action, not a standard query. As such, it does not support configurable filters, sorting, or pagination. Filtering is entirely driven by the input parameters, which also allow limiting the number of results (100 records returned by default, with a maximum of 1000).

### Input Parameters

**Main parameters:**
- **Name:** Legal or commercial name of the entity.
- **Siret:** Unique French establishment identifier (14 digits).

**Advanced parameters (optional and independent from the main parameters):**
- **Address:** Street or address information.
- **City:** City name.
- **ZipCode:** Postal code.
- **NafCode:** French business activity code (APE / NAF).

All parameters can be used independently or combined, depending on the search needs.

### How to use the Third Party Identification service

**GraphQL Query:**

```graphql
query($input: ThirdPartyIdentificationDtoInput!) {
  thirdPartyIdentification(input: $input) {
    businessName
    siretSiren
    address
    city
    zipCode
    nafApeCode
    vatNumber
  }
}
```

**Simple filter:**

```json
{
  "input": {
    "name": "ALPHA"
  }
}
```

**Advanced filter:**

```json
{
  "input": {
    "address": "Boulevard des Tests",
    "city": "Lyon",
    "searchLimit": 10
  }
}
```

**Example Response:**

```json
{
  "data": {
    "thirdPartyIdentification": [
      {
        "businessName": "ALPHA SERVICES FRANCE",
        "siretSiren": "12345678900011",
        "address": "12 Boulevard des Tests",
        "city": "Lyon",
        "zipCode": "69003",
        "nafApeCode": "47.91A",
        "vatNumber": "FR12123456789"
      },
      {
        "businessName": "ALPHA DISTRIBUTION",
        "siretSiren": "98765432100022",
        "address": "14 Boulevard des Tests",
        "city": "Lyon",
        "zipCode": "69003",
        "nafApeCode": "46.90Z",
        "vatNumber": "FR34987654321"
      }
    ]
  }
}
```

### thirdPartyIdentification Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| name | String(200) | *Required | Legal or commercial name of the entity (contains search) |
| siret | String(14) | *Required | French establishment identifier (14 digits) or SIREN (9 digits) |
| address | String(200) | - | Street or address filter |
| city | String(100) | - | City name |
| zipCode | String(10) | - | Postal code |
| nafCode | String(5) | - | French activity code (APE / NAF) |
| searchLimit | Int | - | Maximum number of returned records (default 100, max 1000) |

### thirdPartyIdentification Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| businessName | String | - | Legal or business name |
| siretSiren | String | - | SIRET (14 digits) or SIREN (9 digits) depending on the match |
| address | String | - | Address |
| city | String | - | City |
| zipCode | String | - | Postal code |
| nafApeCode | String | - | French activity code (NAF / APE) |
| vatNumber | String | - | French identification VAT number |

---

## Organization Accounting Setup

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | organizationAccountingSetupByOrgId |

### Description

The organizationAccountingSetupByOrgId operation allows developers to fetch detailed accounting setup information for a specific organization. It is essential for configuring the organization's accounting parameters, ensuring compliance with regional requirements, and streamlining financial operations.

The details retrieved include:
- Default Accounts (e.g., Customer, Supplier, Bank)
- Sub-account maximum length
- Tax regime and annual accounts model

### How to use organizationAccountingSetupByOrgId

**GraphQL Query:**

```graphql
query ($id: ID!) {
  organizationAccountingSetupByOrgId(id: $id) {
    defaultCustomersAccountId
    defaultSuppliersAccountId
    subAccountingLength
    taxRegime
    annualAccountsModel
    defaultBankAccountId
    defaultBankJournalTypeId
    defaultCashAccountId
    defaultCashJournalTypeId
    defaultInputVATAccountId
    defaultOutputVATAccountId
    defaultSaleProductsAccountId
    defaultProfitAccountCFId
    defaultLossAccountCFId
    defaultOpeningAccountCFId
    defaultOpeningJournalCFId
    defaultResultAccountCFId
    defaultCreditorsAccountCFId
    defaultDebtorsAccountCFId
    intracommunityAccountId
    foreignAccountId
    openItemsAutomaticAllocation
    defaultPayrollJournalTypeId
    defaultBankingAdjustmentsId
    defaultBankingTransactionsId
    defaultPayrollSessionId
  }
}
```

**GraphQL Variables:**

```json
{
  "id": "{{X-OrganizationId}}"
}
```

**Example Response:**

```json
{
  "data": {
    "organizationAccountingSetupByOrgId": {
      "defaultCustomersAccountId": "e770b78c-0135-4f63-b875-b8bace1b4c8f",
      "defaultSuppliersAccountId": "e770b78b-ffaa-4f63-b875-b8bace1b4c8f",
      "subAccountingLength": 6,
      "taxRegime": "GENERAL",
      "annualAccountsModel": "UNDEFINED",
      "defaultBankAccountId": "136a9510-94cb-4d49-b205-39718346cba8",
      "defaultBankJournalTypeId": "136a9510-7cba-4d49-b205-39718346cba8",
      "defaultCashAccountId": "136a9510-9231-4d49-b205-39718346cba8",
      "defaultCashJournalTypeId": "136a9510-7cbf-4d49-b205-39718346cba8",
      "defaultInputVATAccountId": "136a9510-9333-4d49-b205-39718346cba8",
      "defaultOutputVATAccountId": "136a9510-91ff-4d49-b205-39718346cba8",
      "defaultSaleProductsAccountId": "136a9510-93d6-4d49-b205-39718346cba8",
      "defaultProfitAccountCFId": "e770b78c-1337-4f63-b875-b8bace1b4c8f",
      "defaultLossAccountCFId": "e770b78c-7337-4f63-b875-b8bace1b4c8f",
      "defaultOpeningAccountCFId": "e770b78c-3337-4f63-b875-b8bace1b4c8f",
      "defaultOpeningJournalCFId": "e770b78c-9337-4f63-b875-b8bace1b4c8f",
      "defaultResultAccountCFId": "e770b78c-5337-4f63-b875-b8bace1b4c8f",
      "defaultCreditorsAccountCFId": "a1234567-89ab-cdef-0123-456789abcdef",
      "defaultDebtorsAccountCFId": "b9876543-21fe-dcba-0987-654321fedcba",
      "intracommunityAccountId": "e770b78c-5337-4f63-b875-b8bace1b4c8f",
      "foreignAccountId": "e770b78c-6337-4f63-b875-b8bace1b4c8f",
      "defaultPayrollJournalTypeId": "5e1ae58d-287f-4d84-bfd2-6244bfadc00c",
      "openItemsAutomaticAllocation": true,
      "defaultBankingAdjustmentsId": "9c71b78c-0135-4f63-b875-b8bace1b4c8f",
      "defaultBankingTransactionsId": "8b62a123-ffaa-4f63-b875-b8bace1b4c8f",
      "defaultPayrollSessionId": "8b62a123-0135-4f63-b875-b8bace1b4c8f"
    }
  }
}
```

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### organizationAccountingSetupByOrgId Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| organizationId | UUID | - | Organization Id |
| organizationSocialName | String | - | Business name |
| defaultCustomersAccountId | UUID | - | Default Customer account Id |
| defaultSuppliersAccountId | UUID | - | Default Supplier account Id |
| defaultPrepaymentCustomerAccountId | UUID | - | Default Pre Payment Customer account Id |
| subAccountingLength | Int | - | Sub accounts max length (max 13) |
| taxRegime | Enum | - | Values: UNDEFINED, BA, BNC, GENERAL |
| annualAccountsModel | Enum | - | Values: UNDEFINED, DATEV_SKR03, DATEV_SKR04, NORMAL, PYME, PYME2025 |
| defaultBankAccountId | UUID | - | Default bank account Id |
| defaultCashAccountId | UUID | - | Default cash account Id |
| defaultBankingAdjustmentsId | UUID | - | Used for bank fee, interest, or other adjustments during reconciliation |
| defaultBankingTransactionsId | UUID | - | Used for reconciling bank transactions not linked to customers or suppliers |
| defaultBankJournalTypeId | UUID | - | Default bank journal type Id |
| defaultCashJournalTypeId | UUID | - | Default cash journal type Id |
| defaultInputVATAccountId | UUID | - | Default input VAT account Id |
| defaultOutputVATAccountId | UUID | - | Default output VAT account Id |
| defaultPurchaseProductsAccountId | UUID | - | Default purchase products account Id |
| defaultSaleProductsAccountId | UUID | - | Default sale products account Id |
| intracommunityAccountId | UUID | - | Intracommunity account Id |
| foreignAccountId | UUID | - | Foreign account Id |
| defaultProfitAccountCFId | UUID | - | Default profit account Carry Forward Id |
| defaultLossAccountCFId | UUID | - | Default loss account Carry Forward Id |
| defaultOpeningAccountCFId | UUID | - | Default opening account Carry Forward Id |
| defaultOpeningJournalCFId | UUID | - | Default opening journal Carry Forward Id |
| defaultResultAccountCFId | UUID | - | Default result account Carry Forward Id |
| defaultCreditorsAccountCFId | UUID | - | Default creditors account Carry Forward Id |
| defaultDebtorsAccountCFId | UUID | - | Default debtors account Carry Forward Id |
| openItemsAutomaticAllocation | Boolean | - | Open items automatic allocation |
| defaultPayrollJournalTypeId | UUID | *Required | Default journal Payroll Id (only if Payroll is integrated) |
| defaultPayrollSessionId | UUID | *Required | Default session Id for journal Payroll (only if Payroll is integrated) |
| defaultFixedAssetJournalTypeId | UUID | - | Default fixed asset journal type Id (added 2026-05) |
| defaultFinancialDiscountAccountId | UUID | - | Default financial discount account Id (added 2026-06, PT legislation) |

### Info

- **defaultPrepaymentCustomerAccountId:** This accounting code is used to post the accounting entry to an advance payment and deposit received account for advance invoices.
- **taxRegime:** Tax Treatment and accounting options vary depending on the organization's legislation:
  - FR: General, BA IR in revenue expenditure, BNC IR in revenue expenditure
  - DE: Not used
  - ES: Not used
- **annualAccountsModel:** Specifies the applicable annual accounts model:
  - FR: Not used
  - DE: DATEV SKR03 (Process-based), DATEV SKR04 (Function-based)
  - ES: NORMAL (Standard), PYME (Small and Medium-sized Enterprises)
- **defaultXXXId fields by market:**
  - FR: All valid except defaultResultAccountCFId, defaultCreditorsAccountCFId, defaultDebtorsAccountCFId, defaultPayrollSessionId
  - DE: All valid except defaultResultAccountCFId
  - ES: All valid except defaultProfitAccountCFId, defaultLossAccountCFId, defaultOpeningAccountCFId, defaultCreditorsAccountCFId, defaultDebtorsAccountCFId, defaultPayrollJournalTypeId, defaultPayrollSessionId
- **openItemsAutomaticAllocation:** Automatically match entries with the oldest open items.

---

## Organization Sales Setup

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | organizationSalesSetupByOrgId |

### Description

The organizationSalesSetupByOrgId operation allows developers to fetch detailed sales setup information for a specific organization. This setup includes key parameters necessary for managing sales, purchase invoices, inventory, and discount configurations effectively.

The details retrieved include:
- Posting permissions for sales and purchase invoices
- Discount groups and tariff IDs
- Default journal types and session IDs for purchase and sales invoices
- Stock, serial number, and batch number configurations
- Valuation methods and tax group settings

### How to use organizationSalesSetupByOrgId

**GraphQL Query:**

```graphql
query ($id: ID!) {
  organizationSalesSetupByOrgId(id: $id) {
    salesInvoiceDefaultPresetTextId
    salesCreditNoteDefaultPresetTextId
    salesDeliveryNoteDefaultPresetTextId
    salesOrderDefaultPresetTextId
    useSalesTracking
    useVATRatesForDOM
    allowPostingSalesInvoice
    defaultSalesPostingInvoiceJournalTypeId
    defaultSalesPostingInvoiceSessionId
    allowPostingPurchaseInvoice
    defaultPurchasePostingInvoiceJournalTypeId
    defaultPurchasePostingInvoiceSessionId
  }
}
```

**GraphQL Variables:**

```json
{
  "id": "{{X-OrganizationId}}"
}
```

**Example Response:**

```json
{
  "data": {
    "organizationSalesSetupByOrgId": {
      "salesInvoiceDefaultPresetTextId": "01234567-89ab-cdef-0123-456789abcdef",
      "salesCreditNoteDefaultPresetTextId": "41234567-89ab-cdef-0123-456789abcdef",
      "salesDeliveryNoteDefaultPresetTextId": "52234567-89ab-cdef-0123-456789abcdef",
      "salesOrderDefaultPresetTextId": "63234567-89ab-cdef-0123-456789abcdef",
      "useSalesTracking": true,
      "useVATRatesForDOM": false,
      "allowPostingSalesInvoice": true,
      "defaultSalesPostingInvoiceJournalTypeId": "81234567-89ab-cdef-0123-456789abcdef",
      "defaultSalesPostingInvoiceSessionId": "71234567-89ab-cdef-0123-456789abcdef",
      "allowPostingPurchaseInvoice": false,
      "defaultPurchasePostingInvoiceJournalTypeId": "a1234567-89ab-cdef-0123-456789abcdef",
      "defaultPurchasePostingInvoiceSessionId": "882f7366-8927-49c3-aa01-2162ea3d2439"
    }
  }
}
```

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### organizationSalesSetupByOrgId Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |

**Sales:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| salesDeliveryNoteDefaultPresetTextId | UUID | - | Id of the default preset text for delivery notes |
| salesOrderDefaultPresetTextId | UUID | - | Id of the default preset text for orders |
| salesQuoteDefaultPresetTextId | UUID | - | Id of the default preset text for sales quotes |
| salesInvoiceDefaultPresetTextId | UUID | - | Id of the default preset text for sales invoices |
| salesCreditNoteDefaultPresetTextId | UUID | - | Id of the default preset text for sales credit notes |
| salesReceiptDefaultPresetTextId | UUID | - | Id of the default preset text for sales receipts (added 2026-06; unused for FR) |
| useSalesTracking | Boolean | - | Indicates if sales tracking is enabled |
| useVATRatesForDOM | Boolean | - | Indicates if VAT rates for DOM are used |

**Spain (simplified invoice) — read-only (live since 2026-05):**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| askGenerateNonIdentifiedSalesInvoicesByDefault | Boolean | Read-only | When true, simplified invoices are used for individual customers by default. ES only. |
| nonIdentifiedSalesInvoiceMaxAmount | Decimal | Read-only | Warning limit for simplified invoice amounts (e.g. 400 for the general €400 VAT-included limit). ES only. |

**Posting Sales Invoices:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| allowPostingSalesInvoice | Boolean | - | Allow posting sales invoices |
| defaultSalesPostingInvoiceJournalTypeId | UUID | - | Default journal type ID for sales invoices |
| defaultSalesPostingInvoiceSessionId | UUID | - | Default session ID for sales invoices |

**Posting Purchase Invoices:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| allowPostingPurchaseInvoice | Boolean | - | Allow posting purchase invoices |
| defaultPurchasePostingInvoiceJournalTypeId | UUID | - | Default journal type ID for purchase invoices |
| defaultPurchasePostingInvoiceSessionId | UUID | - | Default session ID for purchase invoices |

### Info

- **useVATRatesForDOM:** Allows selecting VAT rates for DOM in sales entities. Not used for Spanish or German market.
- **defaultSalesPostingInvoiceSessionId:** Not used for French or Spanish Market. Used to define the Session preference for posting a sales invoice to your accounting records (DE only).
- **defaultPurchasePostingInvoiceSessionId:** Not used for French or Spanish Market. Used to define the Session preference for posting a purchase invoice to your accounting records (DE only).

---

## Organization Sales Setup Docs (Document Customization)

> **Source:** <https://developer.sage.com/sageactive/resources/organizationsalessetupdocscustomization>
> New query — live since the 2026-06 release.

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | organizationSalesSetupDocsCustomizationByOrgId |

### Description

Returns the sales document customization settings configured for the organization set in `X-OrganizationId`. These settings control the layout, style, content, and export options applied to sales documents (quotes, orders, delivery notes, invoices, credit notes).

### organizationSalesSetupDocsCustomizationByOrgId Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |

**Layout:**

| Field | Type | Notes |
|-------|------|-------|
| logoSizePercentage | Int | Logo size percentage |
| salesDocumentCustomizationLogoPosition | Enum | Values: TOP_LEFT, TOP_RIGHT |
| salesDocumentCustomizationCompanyDetailsPosition | Enum | Values: BELOW_LOGO, NEXT_TO_LOGO |
| salesDocumentCustomizationCustomerAddressPosition | Enum | Values: LEFT, RIGHT |
| salesDocumentCustomizationDescriptionWidth | Enum | Values: COLUMN_WIDTH, FULL_WIDTH |

**Style:**

| Field | Type | Notes |
|-------|------|-------|
| salesDocumentCustomizationStyle | Enum | Values: DEFAULT |
| salesDocumentCustomizationFontFamilyName | Enum | Values: ARIAL, CALIBRI, COMIC_SANS_MS, COURIER, GEORGIA, SOURCE_SANS_PRO, TAHOMA, TIMES_NEW_ROMAN, VERDANA |
| salesDocumentCustomizationFontSize | Enum | Values: LARGE, MEDIUM, SMALL |
| salesDocumentCustomizationFontColor | String | Default font colour (HEX code) |
| salesDocumentCustomizationBackgroundImageFit | Enum | Values: SCALE_HEIGHT, SCALE_WIDTH |
| salesDocumentCustomizationBackgroundImageOpacity | Int | Background image opacity |

**Colour scheme:**

| Field | Type | Notes |
|-------|------|-------|
| applyExportColorToAllDocuments | Boolean | Apply the same export colour to all document types |
| salesQuotesExportColor | String | Export colour for quotes (HEX code) |
| salesOrdersExportColor | String | Export colour for orders (HEX code) |
| salesDeliveryNotesExportColor | String | Export colour for delivery notes (HEX code) |
| salesInvoicesExportColor | String | Export colour for invoices (HEX code) |
| salesCreditNotesExportColor | String | Export colour for credit notes (HEX code) |

**Items table (per document type — Quotes / Orders / Delivery notes / Invoices / Credit notes):**

| Field pattern | Type | Notes |
|---------------|------|-------|
| showProductCode{DocType} | Boolean | Show product or service code (DocType ∈ Quotes, Orders, DeliveryNotes, Invoices, CreditNotes) |
| showProductName{DocType} | Boolean | Show product or service name |
| showProductDescription{DocType} | Boolean | Show product or service description |
| showPricingAndAmountDeliveryNotes | Boolean | Show pricing and amount on delivery notes (delivery notes only) |
| showDeliveryAddressInvoices / showDeliveryAddressCreditNotes | Boolean | Show the delivery address (invoices / credit notes only) |
| sales{DocType}IntroText | String | Personalised introduction text — default for `comments` when creating a new document of that type |

**Footer details and copies:**

| Field | Type | Notes |
|-------|------|-------|
| specialMention | String | Final notes displayed at the end of sales documents. Org-level default; merge it with the customer's `specialMention` when creating a sales invoice |
| legalTextFirstLine | String | First line of footer legal or contact information |
| legalTextSecondLine | String | Second line of footer legal or contact information |
| numberOfCopiesForQuotes / ...Orders / ...Invoices / ...CreditNotes / ...Receipts | Int | Number of copies generated per document type |

---

## Organization IRPF Setup (ES only)

> **Source:** <https://developer.sage.com/sageactive/resources/organizationirpfsetup>
> New query — live since the 2026-06 release. Available for ES legislation only.

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | organizationIrpfSetupByOrgId |

### Description

Returns the IRPF (Spanish withholding tax) setup configured for the organization set in `X-OrganizationId`: the configured withholding rate with its identifier, label, and percentage used when calculating withholding amounts on sales invoices and related documents.

Used together with `useWithholdingForSales` at organization level and the IRPF-related fields on sales documents and lines (`totalLiquidNoWithholding`, `totalWithholding`, `withholdingPercentage`) and products (`useWithholding`).

### organizationIrpfSetupByOrgId Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| name | String | - | Name of the IRPF withholding rate (Spain only) |
| percentage | Decimal | - | Withholding percentage (IRPF) applied (Spain only) |

---

## Organization Global Setup (FR only)

> **Source:** <https://developer.sage.com/sageactive/resources/organizationglobalsetup>
> New query — live since the 2026-06 release. Available for FR legislation only.

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | organizationGlobalSetupByOrgId |

### Description

Returns the global setup configured for the organization set in `X-OrganizationId`. For French organizations this includes the three mandatory legal mentions (mentions légales obligatoires) configured under Settings > Business > Legal obligations. They are always present on sales invoices and are exposed on invoices as BT-22 (Invoice note) using UN/CEFACT qualifier codes AAB, PMD, and PMT.

| Sage Active UI section | API field | UN/CEFACT code | BT-22 purpose |
|------------------------|-----------|----------------|---------------|
| Early payment conditions | legalMention1 | AAB | Discount, or absence of discount, for early payment |
| Late payment conditions | legalMention2 | PMD | Penalties in accordance with the company's payment terms |
| Legal fixed compensation | legalMention3 | PMT | Fixed penalty of 40 EUR for collection costs |

### organizationGlobalSetupByOrgId Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| legalMention1 | String(200) | - | Early payment conditions (AAB / BT-22). Default: "Les règlements reçus avant la date d'échéance ne donneront pas lieu à escompte." |
| legalMention2 | String(200) | - | Late payment conditions (PMD / BT-22). Default: "Tout retard de paiement entraîne l'exigibilité de pénalités calculées sur la base de trois fois le taux d'intérêt légal." |
| legalMention3 | String(200) | - | Legal fixed compensation for collection costs (PMT / BT-22). Default: "Indemnité forfaitaire pour frais de recouvrement en cas de retard de paiement : 40 €." |

---

## Email Templates

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | emailTemplates filtered by id |
| POST | List | Query | emailTemplates |

### Description

Represents a collection of email templates used for sending automated emails related to business documents.
An Email Template defines the subject, content, and recipients of emails generated by the system, ensuring consistent and professional communication with customers and internal users.

Each email template is associated with a specific document type, such as quotes, orders, delivery notes, or invoices.
Email templates can be customized to include default recipients, carbon copies, and message content.

These templates can be used as a source of information when calling the documentPdfEmail service to generate and send emails together with the produced PDF documents.

> **Important:** Email templates are not automatically applied when sending a document by email. The client application must first read the template and then manually copy the relevant fields (subject, message, cc, bcc, etc.) into the documentPdfEmail input.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### emailTemplates Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation Date |
| modificationDate | DateTime | System | Modification Date |
| documentType | Enum | Read-only | Values: INVOICES_DUE_IN_THE_NEXT_DAYS, NEW_DELIVERY_NOTE, NEW_INVOICE, NEW_ORDER, NEW_QUOTE, PENDING_INVOICE_PAYMENT_REMINDER, PENDING_QUOTE |
| name | String | Read-only | Template name |
| isDefault | Boolean | - | Indicates whether the template is the default |
| sendMeCopy | Boolean | - | Sends a copy to the sender |
| sendAdditionalCopy | Boolean | - | Sends an additional copy |
| cc | String | - | CC recipient's email addresses |
| bcc | String | - | BCC recipient's email addresses (hidden copy) |
| emailSubject | String | Read-only | Email subject |
| emailMessage | String | Read-only | Email body content |

---

## Operational Number Preset Texts

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | operationalNumberPresetTexts filtered by id |
| POST | List | Query | operationalNumberPresetTexts |

### Description

Represents the Operational Number Preset Texts in the API context, used for defining numbering prefixes for different types of sales documents.
These presets provide flexibility in customizing document numbering.
The Organization Sales Setup includes two fields to define the default presets: one for invoices and another for credit notes.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### operationalNumberPresetTexts Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| creationDate | DateTime | System | Creation date |
| modificationDate | DateTime | System | Modification date |
| description | String | - | Description of the preset text |
| isDefault | Boolean | - | Indicates if it is the default preset |
| operation | Enum | - | Values: NOT_SPECIFIED, DELIVERY_NOTE, INVOICE, ORDER, QUOTE |
| operationalNumberSeriesType | Enum | - | Values: NOT_SPECIFIED, NORMAL, RECOVERY_MANUALLY, RECOVERY_IMPORTED. Operational number series type (added 2026-06, PT legislation) |
| presetText | String | - | Preset text (prefix applied to operational numbers) |

### Info

- **Description:** Provides details about the operational number preset text.
- **Is Default:** Indicates that this preset will be the default prefix proposed when creating a document of the type specified in Operation.
- **Operation:** Specifies the type of operation associated with this preset, such as Quote, Order, Delivery Note or Invoice.
- **Preset Text:** Defines the predefined text or prefix applied to operational numbers.

---

## Unit of Measurement Sales Setups

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | unitOfMeasurementSalesSetups filtered by id |
| POST | List | Query | unitOfMeasurementSalesSetups |

### Description

Represents the configuration feature for the standardized list of UnitsOfMeasurements in the API context, used for defining product quantities in the product interface.
Each unit of measurement can be enabled or disabled, and for those used, associated with a specific Product Category.

See also Unit of Measurements, which describes the resource for directly retrieving the standard list of units.

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-TenantId | **Deprecated.** Current tenant id |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### unitOfMeasurementSalesSetups Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id |
| active | Boolean | - | Indicates if active |
| category | Enum | - | Values: NOT_SPECIFIED, CONCEPT, FEE, PRODUCT, PRODUCT_AND_SERVICE, SERVICE |
| unitOfMeasurement | UnitOfMeasurement | - | Fields of Unit of Measurement (DATALOADER) |
| unitOfMeasurementId | UUID | - | Unit of Measurement ID |

### Info

- **Active:** Enables or disables the use of a unit of measurement. This setting can be configured from the Settings interface in Sage Active.
- **Category:** Indicates whether a unit of measurement is available for a Product-type item, a Service-type item, or both. This setting can also be configured from the Settings interface.

---

## Localized Enum Values

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Action (Query) | localizedEnumValues |

### Description

The localizedEnumValues service allows you to retrieve the localized labels of enumeration values by providing the name of the enumeration and the target language.
This service is particularly useful to dynamically populate UI dropdowns or filters with proper localized labels.

The language parameter is mandatory and must be provided in one of the following formats:
- Short format: fr, en, es, de
- Full format: as returned by applicationLanguageCode in userProfile, e.g., fr-FR

The list of available enumeration names can be retrieved by querying the GraphQL schema.

### Required Parameters

- **enumName:** The technical name of the enumeration to localize.
- **language:** The target language for the returned labels. Must be in short format (fr, en, es, de) or full format (fr-FR).

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| x-api-key | Primary or secondary subscription key of your app |

### How to use localizedEnumValues

**GraphQL Query:**

```graphql
query($enumName: String!, $language: String!) {
  localizedEnumValues(enumName: $enumName, language: $language) {
    value
    label
  }
}
```

**GraphQL Variables:**

```json
{
  "enumName": "UnitOfMeasurementNameType",
  "language": "de"
}
```

**Example Response:**

```json
{
  "data": {
    "localizedEnumValues": [
      { "value": "CUBIC_METRE", "label": "Kubikmeter" },
      { "value": "DAY", "label": "Tag" },
      { "value": "GRAM", "label": "Gramm" },
      { "value": "HOUR", "label": "Stunde" },
      { "value": "KILOGRAM", "label": "Kilogramm" },
      { "value": "KILOMETRE", "label": "Kilometer" },
      { "value": "LITRE", "label": "Liter" },
      { "value": "METRE", "label": "Meter" },
      { "value": "MILLIGRAM", "label": "Milligramm" },
      { "value": "MILLILITRE", "label": "Milliliter" },
      { "value": "MONTH", "label": "Monat" },
      { "value": "NIGHT", "label": "Nacht" },
      { "value": "NOT_UNIT", "label": "Nicht angegeben" },
      { "value": "PACK", "label": "Packung" },
      { "value": "PERCENTAGE", "label": "Prozent" },
      { "value": "SQUARE_METRE", "label": "Quadratmeter" },
      { "value": "TONNE", "label": "Tonne" },
      { "value": "UNIT", "label": "Stueck" }
    ]
  }
}
```

### Error Handling

If the enum name or the language is unsupported or invalid, the API responds with informative messages:

```json
{
  "errors": [
    {
      "message": "Unrecognized enumeration name 'AccountsLevel'. Please check the enumeration name and try again."
    }
  ],
  "data": null
}
```

```json
{
  "errors": [
    {
      "message": "The specified language 'XX' is unsupported."
    }
  ],
  "data": null
}
```

### localizedEnumValues Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| enumName | String(250) | *Required | The name of the enumeration to localize |
| language | String(2 or 5) | *Required | Required language code (short: fr, en, es, de or full: fr-FR) |

### localizedEnumValues Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| value | String | - | Enum value (technical name) |
| label | String | - | Localized label for the enum value |

---

## Localized Error Message

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Action (Query) | localizedErrorMessage |

### Description

The localizedErrorMessage service retrieves a localized error message based on a given error code and a mandatory language parameter, ensuring an enhanced user experience with clear and specific error information.
This service supports the following languages: French, Spanish, English, and German.

The language parameter is mandatory and must be provided in one of the following formats:
- Short format: fr, en, es, de
- Full format: as returned by applicationLanguageCode in userProfile, e.g., fr-FR

### Required Parameters

- **errorCode:** The unique identifier for the business error.
- **language:** A required parameter specifying the language in which the error message should be localized. Must be in short format (fr, en, es, de) or full format (fr-FR).

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| x-api-key | Primary or secondary subscription key of your app |

### How to use localizedErrorMessage

**GraphQL Query:**

```graphql
query($errorCode: String!, $language: String!) {
  localizedErrorMessage(errorCode: $errorCode, language: $language) {
    message
  }
}
```

**GraphQL Variables:**

```json
{
  "errorCode": "sales.businessErrors.codeCannotContainBlankSpaces",
  "language": "en"
}
```

**Example Response:**

```json
{
  "data": {
    "localizedErrorMessage": {
      "message": "You cannot enter a code that includes blank spaces. Enter a code that only contains letters or numbers. "
    }
  }
}
```

### Error Handling

If errorCode or language is unsupported or invalid, the API responds with informative messages:

```json
{
  "errors": [
    {
      "message": "Unrecognized error code 'Xaccounting.businessErrors.accountingAccountApplyVatFalseAndDatevEnabledDE'. Please check the error code and try again."
    }
  ],
  "data": null
}
```

```json
{
  "errors": [
    {
      "message": "The specified language 'XF' is unsupported."
    }
  ],
  "data": null
}
```

### localizedErrorMessage Input Parameters

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| errorCode | String(250) | *Required | Error code to be localized |
| language | String(2 or 5) | *Required | Required language code (short: fr, en, es, de or full: fr-FR) |

### localizedErrorMessage Response

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| message | String | - | Localized error message |
| errorCode | String | - | The error code used for the message |
| language | String | - | Language of the returned message |

---

## Common Resources Across Organizations

This section discusses resources that are common to all organizations within a single tenant:
- Organizations
- Countries
- Zip Codes
- Document Types
- Unit of Measurements
- Currencies

> **Important Note:** Failure to include the X-OrganizationId header in your API requests for these resources will result in an error, as these are not shared resources but are specific to an organization.

---

## Communication Preferences

This section allows you to manage your subscription to communications related to the Sage Active Public API, including:
- Notifications about new versions, along with links to release notes.
- Important updates regarding breaking changes or compatibility issues.
- Other relevant information regarding the use of the service.
