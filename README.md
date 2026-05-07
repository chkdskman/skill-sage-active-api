# Sage Active API — Skill / Plugin for AI Coding Assistants

Complete reference for **Sage Active Public API V2** (GraphQL, built with Hot Chocolate).
Works with **Claude Code**, **Cursor**, and **OpenAI Codex / ChatGPT**.

## What this plugin provides

- **Getting Started guide** for bootstrapping new Sage Active integrations
- **OAuth2 authentication** documentation for all 3 app types (web server, mobile/PKCE, SPA)
- **Complete entity references** with ALL fields, types, constraints, and business rules
- **GraphQL patterns** — pagination, filtering, sorting, update patterns
- **Legislation-specific rules** for FR (France), ES (Spain), and DE (Germany)
- **Source URLs** for every entity page so agents can check for updated documentation

## Covered entities

Customers, Suppliers, Employees, Products, Sales Quotes, Sales Orders, Sales Delivery Notes, Sales Invoices, Purchase Invoices, Accounting Accounts, Accounting Entries, Trial Balance, Balance Sheet, P&L, Organizations, Users, Countries, Currencies, Taxes, Bank Accounts, File Management, Aggregations, Lists, KPIs, and more.

---

## Installation

### Claude Code (Plugin)

Claude Code supports plugins natively. Install from the repo URL:

```bash
claude plugin add https://github.com/chkdskman/skill-sage-active-api.git
```

Or install from a local clone:

```bash
git clone https://github.com/chkdskman/skill-sage-active-api.git
claude plugin add ./skill-sage-active-api
```

The skill triggers automatically when you mention "Sage Active", "sage api", "sage graphql", or any Sage Active entity name.

### Cursor (Project Rules)

Cursor uses `.cursor/rules/` files to give context to the AI. To install:

1. Clone the repo into your project (or as a submodule):

```bash
git clone https://github.com/chkdskman/skill-sage-active-api.git .cursor/sage-active
```

2. Create a rule file `.cursor/rules/sage-active.mdc`:

```markdown
---
description: Sage Active Public API V2 (GraphQL) reference — authentication, entities, fields, legislation rules (FR/ES/DE)
globs:
alwaysApply: false
---

When working with Sage Active API, use the reference documentation in `.cursor/sage-active/skills/sage-active-api/`.

Key files:
- `SKILL.md` — Overview, endpoints, entity quick reference, core patterns
- `references/00-endpoints-auth.md` — OAuth2 authentication (3 flows)
- `references/01-graphql-patterns.md` — Pagination, filtering, sorting, errors
- `references/02-customers.md` through `references/11-purchase-invoices.md` — Entity CRUD
- `references/12-accounting-accounts.md` through `references/14-accounting-reports.md` — Accounting
- `references/15-reference-data.md` — Organizations, currencies, taxes, config
- `references/16-file-management.md` — File upload/download
- `references/17-aggregations-lists.md` — Reporting
- `references/18-legislation-rules.md` — Consolidated FR/ES/DE rules
- `references/19-update-patterns.md` — Update patterns (requestedAction, replaceAll)

Read the relevant reference file before generating Sage Active API code.
```

3. In Cursor, the rule will appear in settings. Enable it for your project.

> **Alternative (simpler):** Copy the `skills/sage-active-api/` folder directly into `.cursor/rules/sage-active-api/` and Cursor will index the content automatically.

### OpenAI Codex / ChatGPT (AGENTS.md)

Codex uses an `AGENTS.md` file at the root of the repository for custom instructions.

1. Clone the repo into your project:

```bash
git clone https://github.com/chkdskman/skill-sage-active-api.git docs/sage-active
```

2. Add a reference in your project's `AGENTS.md` (create it if it doesn't exist):

```markdown
## Sage Active API

When working with Sage Active integrations, always consult the reference documentation
in `docs/sage-active/skills/sage-active-api/` before generating code.

- Start with `SKILL.md` for an overview of endpoints, entities, and patterns.
- Read the specific `references/XX-entity.md` file for the entity you are working with.
- Check `references/18-legislation-rules.md` for FR/ES/DE differences.
- Check `references/19-update-patterns.md` for mutation patterns.

API base URLs:
- FR: https://api.fr.active.sage.com/graphql
- ES: https://api.es.active.sage.com/graphql
- DE: https://api.de.active.sage.com/graphql

Authentication: OAuth2 (see references/00-endpoints-auth.md)
Rate limit: 3000 requests/app/minute
```

> **Tip:** For ChatGPT custom GPTs, you can upload the `skills/sage-active-api/` folder as knowledge files.

### Generic (any AI assistant)

The reference files are standard Markdown. You can use them with any AI tool that supports context files:

```bash
git clone https://github.com/chkdskman/skill-sage-active-api.git
```

Key entry point: `skills/sage-active-api/SKILL.md`
All entity details: `skills/sage-active-api/references/`

---

## Source

Documentation sourced from: https://developer.sage.com/sageactive/
API Version: V2 (March 2026 release)

## License

MIT
