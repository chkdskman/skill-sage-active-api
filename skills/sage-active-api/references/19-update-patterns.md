# Update Patterns — requestedAction, replaceAll, Nested Updates

> **Source:** <https://developer.sage.com/sageactive/resources/update>

## Overview

When it comes to updating records in GraphQL, particularly using Hot Chocolate, the logic closely resembles the concept of the PATCH method in RESTful APIs.

> **Remember:** In GraphQL, the act of modification, including updates, aligns with the concept of a mutation. Mutations in GraphQL are carried out using the HTTP POST method, which is consistent with all other mutation and query actions. This ensures a standardized and intuitive approach to making changes to data within the GraphQL schema.

## Evolution of Update Capabilities

The `requestedAction` mechanism and the full patch-style update logic are already available in the current version of the API.

However, support for the `replaceAll` array-based syntax (e.g. `replaceAll: ["CONTACTS"]`) is part of the release. This documentation anticipates that change to help you understand the evolution of the API and prepare for its extended update capabilities.

## Granular Data Modification

The focus is on modifying only the specific fields that need to be changed, rather than sending the entire dataset. This approach ensures efficiency and minimizes unnecessary data transfer.

In a typical update, you only need to send the fields that have changed. This patch-style logic avoids sending unchanged fields, reducing payload size and improving performance.

For example, if you want to update just the `vatNumber` of a customer, your mutation can be minimal and efficient:

```graphql
mutation ($values: CustomerUpdateGLDtoInput!) {
  updateCustomer (input: $values) {
    id
  }
}
```

```json
{
  "values": {
    "id": "{currentId}",
    "vatNumber": "FR11123456789"
  }
}
```

However, when dealing with nested sub-resources, such as a customer's contacts, or the phones belonging to those contacts, the situation becomes more complex. These nested structures are common in various parts of the API, for example:

| Resource Type | Nested Sub-Resources |
|---|---|
| Customers | contacts, phones, emails, socialMedias, addresses, paymentTermLines |
| Suppliers | contacts, phones, emails, socialMedias, addresses, paymentTermLines |
| Employees | contacts, phones, emails, socialMedias |
| SalesQuotes | salesQuoteLines |
| SalesOrders | salesOrderLines |
| SalesDeliveryNotes | salesDeliveryNoteLines |
| SalesInvoices | salesInvoiceLines |
| ... | (and other similar hierarchical structures) |

The `requestedAction` field and the `replaceAll` flag play a crucial role when updating sub-resources, enabling precise control over whether to modify individual items or replace entire collections.

## Update Strategies for Nested Sub-Resources

When dealing with nested sub-resources like contacts or phones, you have two possible strategies:

1. You can use a **granular patch logic**, combining `id` and `requestedAction` to add, modify, or delete individual items.
2. Or, when it's simpler to reset everything, you can use the **`replaceAll` flag** to fully replace an entire collection, either:
   - at a local level (e.g. all phones of a specific contact), or
   - at a global level (e.g. all contacts of a customer, including their phones, emails, etc.).

This hybrid model (patch by default, with optional replace by reset) allows flexibility depending on what the calling system is capable of providing.

### When to Use Each Strategy

- If your system can track exactly what changed (create, update, delete), then the **`requestedAction`** approach is recommended, as it allows precise control over each element.
- If your system does not track precisely what changed (it only knows the final expected list), then **`replaceAll`** becomes a better fit. It's easier to clear the previous values and recreate the full structure in one step.

For example, consider the case of updating a customer who has multiple contacts, and each contact has multiple phones. In a single update operation, you might need to:

- Delete a contact who has left the company
- Create a new contact
- Modify the phone number of an existing contact
- Add or remove phones for specific contacts

All these changes can be expressed together using the `requestedAction` field or simply relying on the presence or absence of `id`, as part of a patch-style mutation.

### Example Using requestedAction

This example demonstrates a patch-style update where:

- The phones of an existing contact are individually updated (modify, delete, create)
- One contact is deleted
- One new contact is added

```json
{
  "id": "{customerId}",
  "contacts": [
    {
      "id": "{contact1Id}",
      "requestedAction": "MODIFY",
      "phones": [
        {
          "id": "{phone1Id}",
          "requestedAction": "MODIFY",
          "number": "01 23 45 67 89"
        },
        {
          "id": "{phone2Id}",
          "requestedAction": "DELETE"
        },
        {
          "requestedAction": "CREATE",
          "number": "06 07 08 09 10",
          "type": "MOBILE"
        }
      ]
    },
    {
      "id": "{contact2Id}",
      "requestedAction": "DELETE"
    },
    {
      "requestedAction": "CREATE",
      "name": "New Contact",
      "phones": [
        {
          "requestedAction": "CREATE",
          "number": "05 55 55 55 55",
          "type": "LANDLINE"
        }
      ]
    }
  ]
}
```

### Example Using replaceAll

This example replaces the entire list of contacts and their sub-collections:

- All existing contacts on the customer are removed
- The list is fully replaced by two new contacts
- Because `replaceAll` includes `CONTACTS`, all sub-collections within each contact such as PHONES, EMAILS, and SOCIAL_MEDIAS are also fully replaced
- Collections are declared as arrays, using the same structure as in creation

```json
{
  "id": "{customerId}",
  "replaceAll": ["CONTACTS"],
  "contacts": [
    {
      "name": "Alice",
      "phones": [
        {
          "number": "06 07 08 09 10",
          "type": "MOBILE"
        }
      ],
      "emails": [
        {
          "emailAddress": "[email protected]",
          "usage": "WORK"
        }
      ]
    },
    {
      "name": "Bob",
      "phones": [
        {
          "number": "01 23 45 67 89",
          "type": "LANDLINE"
        }
      ],
      "socialMedias": [
        {
          "name": "LinkedIn",
          "link": "https://linkedin.com/in/bob"
        }
      ]
    }
  ]
}
```

## Summary Table: Sub-Resource Update Strategies

| Scenario | id | requestedAction | Behavior |
|---|---|---|---|
| New item | not provided | not provided or `CREATE` | Create |
| Modify existing item | provided | not provided or `MODIFY` | Update |
| Delete existing item | provided | `DELETE` (required) | Delete |
| Recreate everything (replaceAll) | not provided | not provided | Full replacement |

## requestedAction: Rules and Requirements

This approach is suitable when the system replicating the data knows the exact changes to apply and can identify each sub-resource by its `id`. Typically, this means the original application has stored the identifiers and is able to send only what was modified, added, or deleted.

### When is requestedAction Necessary?

- **`requestedAction: DELETE`** -- Required. You must explicitly specify this action to delete a sub-resource, along with its `id`.
- **`requestedAction: MODIFY` or `CREATE`** -- Optional. These are optional because the presence or absence of an `id` is usually sufficient:
  - With an `id` -- treated as a modification
  - Without an `id` -- treated as a creation

### Example: Updating a Contact's Phones Using requestedAction

Effect:

- The phone with `id = {phone1Id}` is modified.
- The phone with `id = {phone2Id}` is deleted.
- A new phone is created.
- The rest of the contact and other phones remain unchanged.

```json
{
  "id": "{customerId}",
  "contacts": [
    {
      "id": "{contactId}",
      "phones": [
        {
          "id": "{phone1Id}",
          "number": "01 23 45 67 89"
        },
        {
          "id": "{phone2Id}",
          "requestedAction": "DELETE"
        },
        {
          "number": "06 07 08 09 10",
          "type": "MOBILE"
        }
      ]
    }
  ]
}
```

## replaceAll: Rules and Requirements

This approach is ideal when the system replicating the data does not track individual changes or identifiers, but only knows the final expected state. For instance, it may detect that the customer has changed but cannot identify what exactly changed in the contact or phone lists. In such cases, fully replacing the collection is often simpler and safer.

The `replaceAll` flag is a list of top-level fields declared at the root of the mutation payload. It defines which collections should be fully replaced by the incoming data.

### 1. Top-Level replaceAll (e.g. `replaceAll: ["CONTACTS"]`)

When a field like `"CONTACTS"` is included in the `replaceAll` array, the entire corresponding collection (e.g. the contacts array) is fully replaced.

- Each element of the collection is treated as a new version, and all nested sub-collections (like phones, emails, socialMedias, etc.) are also recreated from scratch.
- There is no need to add `replaceAll` inside the collection itself or its nested objects -- the flag at the root level is sufficient.
- The system assumes the incoming array represents the full new state of that collection.

### 2. Mixed Logic with Partial Updates

If `replaceAll` does not include a given collection (e.g. contacts), then:

- You must include an `id` and a `requestedAction` (like `MODIFY`) on each object you wish to update.
- In this case, you may selectively apply `replaceAll` within a sub-object (e.g. `replaceAll: ["PHONES"]` inside a contact) if you want to fully replace one of its nested collections.
- This allows hybrid use cases: replacing some collections completely while updating others partially.

### Rules to Keep in Mind

- The `replaceAll` property is a list declared at the root level of the payload. Each entry in the list indicates a top-level collection (like `"CONTACTS"`, `"ADDRESSES"`) that must be fully replaced.
- When a collection is listed in `replaceAll` (e.g. `"CONTACTS"`), the entire array provided for that field replaces the existing data, including all nested sub-resources such as phones, emails, etc.
- You must not specify `replaceAll` inside nested objects (e.g. per-contact phones). The replacement behavior is automatically applied to all nested collections.
- If you do not list a collection in `replaceAll`, you must provide explicit `requestedAction` values (such as `MODIFY`, `CREATE`, `DELETE`) inside each object you want to update.
- Mixing update strategies is possible: you can fully replace one collection via `replaceAll` and partially update another using `requestedAction`.
- Each collection listed in `replaceAll` must be accompanied by its corresponding array (contacts, addresses, etc.) containing the full new state to apply.

> **Important:** Unlike the `requestedAction` logic, where adding a new sub-resource does not require explicitly setting `requestedAction="CREATE"`, the `replaceAll` logic works differently. If you want to replace an existing collection, you must explicitly declare it in the `replaceAll` array (e.g. `replaceAll: ["CONTACTS"]`). Otherwise, the new items you provide are simply appended to the current collection, instead of replacing its full content.

### Example 1: Replacing All Contacts, Addresses and Their Sub-Resources

Effect:

- All existing contacts and addresses are deleted.
- Each contact is recreated with its own sub-resources (phones, emails, socialMedias).
- There is no need to specify `replaceAll` inside contacts or for their nested collections -- the root-level instruction is enough.

```json
{
  "id": "{customerId}",
  "replaceAll": ["CONTACTS", "ADDRESSES"],
  "addresses": [
    {
      "firstLine": "123 Generic street",
      "city": "Generic City",
      "zipCode": "12345",
      "countryIsoCodeAlpha2": "FR"
    }
  ],
  "contacts": [
    {
      "name": "Alice",
      "phones": [
        {
          "number": "06 07 08 09 10",
          "type": "MOBILE"
        }
      ],
      "emails": [
        {
          "emailAddress": "[email protected]",
          "usage": "WORK"
        }
      ]
    },
    {
      "name": "Bob",
      "phones": [
        {
          "number": "01 23 45 67 89",
          "type": "LANDLINE"
        }
      ],
      "socialMedias": [
        {
          "name": "LinkedIn",
          "link": "https://linkedin.com/in/bob"
        }
      ]
    }
  ]
}
```

### Example 2: Replacing Only the Phones, Emails and Social Medias of a Specific Contact

Effect:

- The contact itself is not replaced, only its sub-collections are.
- This assumes the contact is identified (typically by `id`) and updated using `requestedAction: MODIFY`.
- The `replaceAll` flag is declared inside the contact using `"replaceAll": ["PHONES", "EMAILS", "SOCIAL_MEDIAS"]`.

```json
{
  "id": "{customerId}",
  "contacts": [
    {
      "id": "{contactId1}",
      "replaceAll": ["PHONES", "EMAILS", "SOCIAL_MEDIAS"],
      "phones": [
        {
          "number": "06 99 88 77 66",
          "type": "MOBILE"
        },
        {
          "number": "01 11 22 33 44",
          "type": "LANDLINE"
        }
      ],
      "emails": [
        {
          "emailAddress": "[email protected]",
          "usage": "INVOICES"
        }
      ],
      "socialMedias": [
        {
          "name": "X",
          "link": "https://x.com/alice"
        }
      ]
    }
  ]
}
```
