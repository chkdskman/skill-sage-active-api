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

Claude Code installs plugins via marketplaces. This repo ships with its own single-plugin marketplace at `.claude-plugin/marketplace.json`, so you can install it in two commands from inside Claude Code:

```text
/plugin marketplace add chkdskman/skill-sage-active-api
/plugin install sage-active-api@skill-sage-active-api
```

After install, the skill triggers automatically when you mention "Sage Active", "sage api", "sage graphql", or any Sage Active entity name.

To pin a specific branch or tag, append `@<ref>` to the marketplace add command:

```text
/plugin marketplace add chkdskman/skill-sage-active-api@main
```

Local development / testing without installing — clone the repo and load it directly with `--plugin-dir`:

```bash
git clone https://github.com/chkdskman/skill-sage-active-api.git
claude --plugin-dir ./skill-sage-active-api
```

### Cursor (Project Rules)

Cursor uses `.cursor/rules/*.mdc` files (Project Rules) to give context to the AI. The `.md` files in this repo are NOT auto-indexed if you copy them into `.cursor/rules/` — Cursor only loads `.mdc` rule files. The pattern below uses one `.mdc` rule that points at the cloned reference docs:

1. Clone the repo into your project (or add it as a submodule):

```bash
git clone https://github.com/chkdskman/skill-sage-active-api.git .cursor/sage-active
```

2. Create a rule file `.cursor/rules/sage-active.mdc`:

```markdown
---
description: Sage Active Public API V2 (GraphQL) reference. Use when generating, debugging, or reviewing GraphQL queries/mutations against Sage Active, working with Sage Active entities (customers, suppliers, employees, products, sales/purchase documents, accounting), or implementing OAuth2 with Sage Active. Covers FR/ES/DE legislation rules.
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

This is an "Agent Requested" rule (`alwaysApply: false`, no `globs`): the agent decides when to load it based on the `description`, so keep that line specific.

3. (Optional) Add `.cursor/sage-active/` to `.cursorindexignore` so the raw `.md` files do not get double-indexed alongside the rule.

4. In Cursor, the rule appears in **Settings → Rules**. Enable it for your project.

> **Tip:** As an alternative to cloning, you can register the public docs URL with Cursor's **@Docs** feature (Settings → Features → Docs) and reference Sage Active docs via `@Docs` in chat.

### OpenAI Codex (AGENTS.md)

Codex follows the [AGENTS.md](https://agents.md/) open standard. Place an `AGENTS.md` at the root of your project and Codex will read it automatically.

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

> **Cross-project (global):** to make Sage Active docs available in every project, drop the same instructions into `~/.codex/AGENTS.md`. Codex merges global and project instructions.
>
> **Per-subdirectory override:** if only one part of a monorepo touches Sage Active (e.g. `services/billing/`), put the instructions in `services/billing/AGENTS.md` instead of the project root.

### ChatGPT custom GPTs

Upload the `skills/sage-active-api/` folder contents as **Knowledge** files when configuring a custom GPT. Note the OpenAI limits:

- **Max 20 files** per GPT as Knowledge
- **512 MB per file**, up to **2M tokens per text file**
- Storage caps: 25 GB per user, 100 GB per organization

This repo has 21 markdown files (1 `SKILL.md` + 20 references), which is over the 20-file limit. Either drop one of the lower-priority references (e.g. merge `19-update-patterns.md` into `01-graphql-patterns.md`) or concatenate related references into a single file before uploading.

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
