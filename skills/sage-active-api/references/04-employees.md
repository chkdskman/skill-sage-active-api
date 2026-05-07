# Employees — Complete Reference

> **Source URLs:**
> - https://developer.sage.com/sageactive/resources/employees

## HTTP Operations

| HTTP | Operation | Type | Object | DTO |
|------|-----------|------|--------|-----|
| POST | Create | Mutation | createEmployee | EmployeeCreateGLDtoInput |
| POST | Update | Mutation | updateEmployee | EmployeeUpdateGLDtoInput |
| POST | Delete | Mutation | deleteEmployee | EmployeeDeleteGLDtoInput |
| POST | Read | Query | employees filtered by id | — |
| POST | List | Query | employees | — |

## Description

Employee management.

Allows the identification of a third-party Employee within an organization. It offers the option to add contacts within the Employee and their details.

Every Employee must have a code, which, depending on the organization's settings, can be manually entered or automatically generated. In the DE (German) legislation, automatic numbering is mandatory.

> **Warning**
> Depending on the Sage Active solution configuration, creating, updating, or deleting employees may not be allowed.
> For example, in the German legislation, employee management is not available.
> In other legislations, when connected to a payroll solution, employees may only be available in read-only mode, replicated from the payroll system into Sage Active.

The `userAccessPolicyCheck` operation can be used to verify whether `createEmployee`, `updateEmployee`, and `deleteEmployee` operations are allowed (response=true) or denied (response=false).

For comprehensive insights into making precise modifications to records, check out the dedicated page on updates: Key concepts / Updating records.

### Example: Create an Employee

**GraphQL Mutation:**

```graphql
mutation ($values: EmployeeCreateGLDtoInput!) {
  createEmployee(input: $values) {
    id
    code
  }
}
```

**GraphQL Variables:**

```json
{
  "values": {
    "name": "Léon DURANT",
    "shortName": "LEDU",
    "comments": "Employee of the year",
    "addresses": [{
      "firstLine": "123 Generic street",
      "city": "Generic City",
      "zipCode": "12345",
      "countryIsoCodeAlpha2": "FR"
    }]
  }
}
```

**Example Response:**

```json
{
  "data": {
    "createEmployee": {
      "id": "e614a276-7de0-4fd2-a10c-e8c29732b530",
      "code": "2"
    }
  }
}
```

## Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {Current access Token} |
| X-TenantId | Current tenant id (deprecated — use alternative solution) |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

## employees

### Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id. System field that cannot be assigned. |
| creationDate | DateTime | System | Creation Date. System field that cannot be assigned. |
| modificationDate | DateTime | System | Modification Date. System field that cannot be assigned. |
| sourceType | GLOBAL, IMPORT_FILES, PUBLIC_API | System | Indicates the origin of the employee. System field that cannot be assigned. |
| code | String (17) | No (auto or manual) | Code employee. Cannot be modified after creation. Automatically calculated if option is active in Sequence Numbers, otherwise mandatory. |
| name | String (70) | Yes | Employee Name. |
| shortName | String (70) | No | Short Name. |
| disabled | Boolean | No | Inactive. If TRUE the employee can no longer be used, but will remain in existing information. |
| disabledDate | DateTime (date only) | No | Inactive date. Only the date value is considered, time is ignored. |
| status | NONE, DISABLED, ENABLED, GDPR_APPLIED | Read-only | Status. |
| defaultAccountingAccount | AccountingAccount | No | DATALOADER. Fields of AccountingAccount. |
| defaultAccountingAccountId | UUID | No | Ledger General account. Id of the general account in accountingAccount associated with the employee. The subAccountType of the account must be EMPLOYEE. |
| comments | String (2000) | No | Comments. |
| gdprApplied | Boolean | No | GDPR action. |
| gdprAppliedDate | DateTime (date only) | No | GDPR date action. Only the date value is considered, time is ignored. |
| businessArea | BusinessArea | No | DATALOADER. Fields of BusinessArea. |
| businessAreaId | UUID | No | Business areas Id. |
| addresses[] | Array | No | List of addresses. |
| contacts[] | Array | No | List of contacts. |

### Info

- **defaultAccountingAccountId**: Id of the general account in accountingAccount associated with the employee. The subAccountType of the account must be EMPLOYEE.
- **disabled**: If TRUE the employee can no longer be used, but will remain in the existing information.
- **code**: Code of the employee. During creation, it is automatically calculated if the option is active in Sequence Numbers, otherwise it is a mandatory value. In any cases, it is still possible to assign it to enforce a specific value. Forcing a value allows you to retrieve an alphanumeric code that can already identify the Employee, even if automatic numbering mode is active. If you force a value, it must be strictly numeric and adhere to the rule of a number being between 10000 and 69999 for customers, and between 70000 and 99999 for suppliers or employees.

## employees/addresses

> **Attention**: Only one address is authorized, even if the representation is an array of addresses.

### Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id. System field that cannot be assigned. |
| creationDate | DateTime | System | Creation Date. System field that cannot be assigned. |
| modificationDate | DateTime | System | Modification Date. System field that cannot be assigned. |
| countryId | UUID | No | Country Id. |
| country | Country | No | DATALOADER. Fields of Country. |
| countryIsoCodeAlpha2 | String (2) | No | Country Code. ISO2 country code. Can be used for creation as a simple alternative to assign the country by using the ISO2 code directly, rather than the country ID. |
| countryName | String | Read-only | CountryName. |
| name | String (50) | No | Code. |
| firstLine | String (35) | No | First Line. |
| secondLine | String (35) | No | Second Line. |
| city | String (35) | No | Town. |
| zipCode | String (9) | No | Postal Code. |
| province | String (25) | No | Province / Region / Federal State. |
| isMainAddress | Boolean | Read-only | Is the main address. |

### Info

- **countryIsoCodeAlpha2**: ISO2 country code. This field can be used for creation and serves as a simple alternative to assign the country of the address by using the ISO2 code directly, rather than the country ID in the Countries resource.

## employees/contacts

Common to customers, suppliers, employees.

### Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id. System field that cannot be assigned. |
| creationDate | DateTime | System | Creation Date. System field that cannot be assigned. |
| modificationDate | DateTime | System | Modification Date. System field that cannot be assigned. |
| isDefault | Boolean | Computed | Main contact. Dynamically computed, not stored in DB. Cannot be used in filtering or ordering. |
| courtesy | NONE, BLANK, MISS, MR | No | Courtesy. |
| name | String (35) | No | First name (John). |
| surname | String (35) | No | Family name (Smith). |
| jobName | String (35) | No | Job name. |
| jobArea | JobArea | No | DATALOADER. Fields of JobArea. |
| jobAreaId | UUID | No | Job areas Id. |
| emails[] | Array | No | List of emails. |
| phones[] | Array | No | List of phones. |
| socialMedias[] | Array | No | List Social Networking. |

## employees/contacts/emails

### Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id. System field that cannot be assigned. |
| creationDate | DateTime | System | Creation Date. System field that cannot be assigned. |
| modificationDate | DateTime | System | Modification Date. System field that cannot be assigned. |
| emailAddress | String (70) | No | Email address. |
| usage | EMPTY, INVOICES, NOT_SET, OTHERS, PAYMENTS | No | Type. |
| isDefault | Boolean | No | Main mail. |

## employees/contacts/phones

### Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | Id. System field that cannot be assigned. |
| creationDate | DateTime | System | Creation Date. System field that cannot be assigned. |
| modificationDate | DateTime | System | Modification Date. System field that cannot be assigned. |
| number | String (20) | No | Phone. |
| type | EMPTY, FAX, LANDLINE, MOBILE, NOT_SET, SKYPE, WHATS_APP | No | Type. |
| isDefault | Boolean | No | Main phone. |

## employees/contacts/socialMedias

### Fields

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | System | UUID. System field that cannot be assigned. |
| creationDate | DateTime | System | Creation Date. System field that cannot be assigned. |
| modificationDate | DateTime | System | Modification Date. System field that cannot be assigned. |
| name | String (30) | No | Name. |
| link | String (100) | No | Link. |

---
