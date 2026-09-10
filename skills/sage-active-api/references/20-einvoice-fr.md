# Electronic Invoicing — France (eInvoice 🇫🇷)

> Added in the **2026-09** Sage release. **FR legislation only.**
> Source pages:
> - <https://developer.sage.com/sageactive/resources/einvoice>
> - <https://developer.sage.com/sageactive/resources/organizationeinvoicesetup>
> - <https://developer.sage.com/sageactive/resources/retrysalesinvoiceelectronicsubmission>

## Overview

E-invoicing (*facture électronique*) for France covers:

1. **Registration** of the organization with the Sage **Plateforme Agréée (PA)** — configured in the product under *Settings → E-Invoicing and e-reporting*, exposed read-only through `organizationEInvoiceSetupByOrgId`.
2. **Routing addresses on customers** — `eInvoicingAddressType` / `eInvoicingAddress`.
3. **Electronic invoice fields on sales invoices** — sending, plus lifecycle tracking via `eInvoicingStatus` and the `eInvoice[]` entries.
4. **Electronic invoice fields and status values on purchase invoices** — receiving.
5. **`retrySalesInvoiceElectronicSubmission`** — resubmit a sales invoice whose electronic submission failed.

Regulatory context: all VAT-registered businesses in France must be able to **receive** e-invoices from **1 September 2026**. Depending on business size, **sending** also becomes mandatory.

**Prerequisite for every flow below:** `organizationEInvoiceSetupByOrgId.status` must be `Accepted`.

---

## Organization E-Invoice Setup (FR only)

### HTTP Operations

| Method | Operation | Type | Object |
|--------|-----------|------|--------|
| POST | Read | Query | `organizationEInvoiceSetupByOrgId` |

### Headers

| Key | Value |
|-----|-------|
| Authorization | Bearer {access_token} |
| X-OrganizationId | Current organization Id |
| x-api-key | Primary or secondary subscription key of your app |

### organizationEInvoiceSetupByOrgId Fields

| Field | Type | Notes |
|-------|------|-------|
| id | UUID | System field |
| status | `Accepted`, `InProgress`, `Failed` | Registration status with the Plateforme Agréée (PA) |
| statusConnectionDate | DateTime | Date of the connection status |
| connectionId | UUID | Connection identifier |
| mandateNumber | String | Mandate number issued after registration |
| companyInformation | String | Company information as a JSON string |
| authorizedUserFirstName | String | First name of the authorized user |
| authorizedUserLastName | String | Last name of the authorized user |
| authorizedUserEmail | String | Email of the authorized user |
| authorizedUserJob | String | Job title of the authorized user |
| effectiveDate | DateTime | Effective date of the e-invoicing setup |
| salesInvoiceEffectiveDate | DateTime | Effective date for sales invoices |
| eReportingStartDate | DateTime | Start date for e-reporting |
| businessInvoicesToggle | Boolean | Business invoices enabled |
| businessPaymentsToggle | Boolean | Business payments enabled |
| individualCustomerSalesToggle | Boolean | Sales to individual customers enabled |
| paymentsIndividualCustomersToggle | Boolean | Payments from individual customers enabled |
| subscriptionId | String | Subscription identifier |
| subscriptionIdConnected | UUID | Connected subscription identifier |
| subscriptionIdUpdated | UUID | Updated subscription identifier |
| subscriptionIdRemoved | UUID | Removed subscription identifier |
| trackId | String | Tracking identifier |
| creationUserId | UUID | Identifier of the user who created the setup |
| paSirenNumber | String | SIREN number of the Plateforme Agréée |
| paIdentificationNumber | String | Identification number of the Plateforme Agréée |
| softwareBusinessName | String | Business name of the software |
| errorCode | String | Error code when registration / connection failed |
| termsAndConditionsAccepted | Boolean | Terms and conditions accepted |
| termsAndConditionsAcceptedAt | DateTime | When the terms were accepted |
| termsAndConditionsAcceptedInVersion | String | Product / terms version in which they were accepted |

### Info

- **status**
  - `Accepted` — the organization is registered with the Plateforme Agréée; e-invoicing flows are available.
  - `InProgress` — registration / connection in progress.
  - `Failed` — registration / connection failed (see `errorCode`).
- The four e-invoicing **input** fields on sales invoices are only visible when `status = Accepted`.

### Example

```graphql
query {
  organizationEInvoiceSetupByOrgId {
    id
    status
    statusConnectionDate
    mandateNumber
    effectiveDate
    salesInvoiceEffectiveDate
    eReportingStartDate
    errorCode
  }
}
```

---

## Customer Setup — routing addresses

On `customers` (see [02-customers.md](02-customers.md)):

| Field | Type | Notes |
|-------|------|-------|
| eInvoicingAddressType | String | Type of address used to receive electronic invoices |
| eInvoicingAddress | String | Identifier registered in the electronic invoicing directory, used to route invoices |

**`eInvoicingAddressType` values and `eInvoicingAddress` formats**

| Type | Meaning | Example |
|------|---------|---------|
| `fr.siren` | SIREN — general address for the whole company | `123456782` |
| `fr.siren_suffix` | SIREN + suffix — route to a department | `123456782_Accounting` |
| `fr.sirensiret` | SIREN + SIRET — a specific establishment / branch | `123456782_12345678200001` |
| `fr.sirensiret_routing` | SIREN + SIRET + routing code — department inside a branch | `123456782_12345678200001_Bakery` |

Only available under FR legislation and for customers with `customerType = BUSINESS`. Both values are copied by default to the sales invoice (`eInvoicingAddressTypeCustomer` / `eInvoicingAddressCustomer`) when the organization is connected to a Plateforme Agréée.

---

## Sales Invoices — sending

Field tables live in [09-sales-invoices.md](09-sales-invoices.md#electronic-invoicing-fr-only--added-2026-09). Summary:

| Field | Direction | Notes |
|-------|-----------|-------|
| shouldSendViaElectronicInvoicing | Input | `true` when the customer has both routing fields filled |
| eInvoicingAddressTypeCustomer | Input | Defaults from the customer |
| eInvoicingAddressCustomer | Input | Defaults from the customer |
| regulatoryBillingFramework | Input | `B1` / `S1` / `M1` / `B4` / `M4` / `S4` |
| eInvoiceId | Read-only | Electronic invoice identifier |
| eInvoicingStatus | Read-only | Lifecycle status |
| eInvoice[] | Read-only | Electronic invoice entries |

**Flow**

1. Register the organization with the PA (`status = Accepted`).
2. Fill `eInvoicingAddressType` / `eInvoicingAddress` on the customer.
3. Create the sales invoice — the customer routing values are copied onto the invoice; set `regulatoryBillingFramework`.
4. `postSalesInvoice` automatically submits the invoice to the PA when `shouldSendViaElectronicInvoicing = true` and the three input fields are filled.
5. Track `eInvoicingStatus` (and `eInvoice[].status` / `eInvoice[].rejectedReason`).
6. On failure, call `retrySalesInvoiceElectronicSubmission`.

---

## Retry Sales Invoice Electronic Submission (FR only)

### HTTP Operations

| Method | Operation | Type | Object | DTO |
|--------|-----------|------|--------|-----|
| POST | Create | ⚙️ Action (Mutation) | `retrySalesInvoiceElectronicSubmission` | `RetrySalesInvoiceElectronicSubmissionGLDtoInput` |

Retries the electronic submission of a sales invoice to the Sage Plateforme Agréée. Use it when a previous submission failed or must be sent again. Applies to a sales invoice already involved in the French electronic invoicing flow.

### Input Parameters — `RetrySalesInvoiceElectronicSubmissionGLDtoInput`

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| salesInvoiceId | UUID | **Yes** | Unique identifier of the sales invoice |

### Response

| Field | Type | Notes |
|-------|------|-------|
| id | UUID | Identifier of the sales invoice for which the retry was requested — confirms the request was accepted |

### Example

```graphql
mutation RetryESubmission($input: RetrySalesInvoiceElectronicSubmissionGLDtoInput!) {
  retrySalesInvoiceElectronicSubmission(input: $input) {
    id
  }
}
```

```json
{
  "input": { "salesInvoiceId": "3f1a7c58-1b1e-4a1e-9f9a-0e4a2f5b7c11" }
}
```

---

## Purchase Invoices — receiving

See [11-purchase-invoices.md](11-purchase-invoices.md). Fields added 2026-09 (FR only):

| Field | Type | Notes |
|-------|------|-------|
| eInvoiceId | UUID | Read-only — electronic invoice identifier |
| isEInvoice | Boolean | Read-only — the purchase invoice is an electronic invoice |
| isEInvoiceCreditNote | Boolean | Read-only — the electronic invoice is a credit note |
| eInvoicingStatus | Enum | Read-only — standardized e-invoicing lifecycle status |

**New `status` values on `purchaseInvoices` (FR e-invoices)**

| Status | Description |
|--------|-------------|
| AwaitingDecision | Electronic invoice awaiting approval / refusal |
| Refused | Electronic invoice refused |
| PartiallyAccepted | Electronic invoice partially accepted |
| Accepted | Electronic invoice accepted |

`sourceType = E_INVOICING_AP_AUTOMATION` identifies purchase invoices that entered Sage Active through the e-invoicing channel.

---

## eInvoicingStatus enum (shared by sales and purchase invoices)

`NEW`, `CREATED`, `SUBMITTED`, `ISSUE_BY_THE_PLATFORM`, `PAYMENT_RECEIVED`, `COMPLETED`, `RECEIVED_BY_PLATFORM`, `MADE_AVAILABLE`, `IN_HAND`, `APPROVED`, `REFUSED`, `PARTIALLY_APPROVED`, `PAYMENT_SENT`, `REJECTED`, `DISPUTED`, `SUSPENDED`, `FAILED_TO_SEND`, `RECEIVED`.
