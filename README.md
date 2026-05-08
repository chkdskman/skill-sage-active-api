# Sage Active API — Skill / Plugin for AI Coding Assistants

Complete reference for **Sage Active Public API V2** (GraphQL, built with Hot Chocolate).
Works with **Claude Code**, **Cursor**, and **OpenAI Codex**.

## Who is this for?

Developers building applications, integrations, or internal tools **on top of Sage Active**. The plugin loads the full GraphQL API V2 reference into your AI coding agent so you don't have to paste docs into every prompt — your agent already knows the entities, the fields, the OAuth flows, and the per-country quirks.

It is **not** for end users of Sage Active itself. If you only want to use the product, you don't need any of this.

## Before you install

The plugin teaches your agent *how* to call the API. To actually call it you'll need a few things from Sage first — none of this is optional, and none of it is something the agent can do for you:

1. **A Sage Active subscription** in FR, ES, or DE. You need one per country you plan to target.
2. **Developer access.** From *Your Sage Active*, request access to the **Developer Center**. Sage links the developer account to the email on your subscription.
3. **Register your company** in the Developer Center (one-time).
4. **Create an application** and associate it with *Sage Active Public API V2*. You'll get three credentials, all required to call the API:
   - **Client ID**
   - **Client Secret** (renewable from the Developer Center)
   - **Subscription Key** (primary + secondary, so you can rotate without downtime)
5. Pick your **OAuth2 flow** — web server, mobile/PKCE, or SPA. The skill walks your agent through each.

Full official walkthrough: <https://developer.sage.com/sageactive/quickstart/>

Don't have a subscription yet? The Sage Active site has the sign-up flow per country.

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

> ☕ **The "I'm holding a coffee" install:** paste this repo's URL into your coding agent (Claude Code, Codex, Cursor — pick your fighter 🥊) and say *"install this as a skill"*. It'll figure out the rest. The instructions below are for when you want to pin a version, install offline, or — wild idea — actually understand what your robot is about to do.

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

Modern Cursor uses `.cursor/rules/*.mdc` files (Project Rules) to give scoped context to Agent and Inline Edit. Do not use the legacy `.cursorrules` format for new installs. The pattern below keeps the Sage Active docs as normal project documentation and adds one Agent Requested rule that attaches the skill entry point when relevant.

1. Clone the repo into your project (or add it as a submodule):

```bash
git clone https://github.com/chkdskman/skill-sage-active-api.git docs/sage-active
```

2. Create a rule file `.cursor/rules/sage-active.mdc`:

```markdown
---
description: Sage Active Public API V2 (GraphQL) reference. Use when generating, debugging, or reviewing GraphQL queries/mutations against Sage Active, working with Sage Active entities (customers, suppliers, employees, products, sales/purchase documents, accounting), or implementing OAuth2 with Sage Active. Covers FR/ES/DE legislation rules.
globs:
alwaysApply: false
---

When working with Sage Active API, first read the skill entry point:

@docs/sage-active/skills/sage-active-api/SKILL.md

Then read the relevant reference file from `docs/sage-active/skills/sage-active-api/references/` before generating Sage Active API code.

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
```

This is an "Agent Requested" rule (`alwaysApply: false`, no `globs`): Cursor makes it available to the agent, and the agent decides whether to include it based on the `description`.

3. In Cursor, the rule appears in **Cursor Settings → Rules** and in the Agent sidebar when active.

> **Tip:** Cursor also supports root-level `AGENTS.md` as a simple alternative to Project Rules, but `.cursor/rules/*.mdc` is better here because the rule can be Agent Requested instead of always applied to the whole project.

### OpenAI Codex (Skill)

Codex can install this repo directly as a skill from the `skills/sage-active-api/` folder:

```text
$skill-installer install https://github.com/chkdskman/skill-sage-active-api/tree/main/skills/sage-active-api
```

After installing, restart Codex so the new skill is picked up. The skill triggers automatically when you mention "Sage Active", "sage api", "sage graphql", or a Sage Active entity name.

Manual install is also possible: copy `skills/sage-active-api/` into `~/.codex/skills/sage-active-api/`, then restart Codex.

### OpenAI Codex (AGENTS.md alternative)

Codex follows the [AGENTS.md](https://agents.md/) open standard. Place an `AGENTS.md` at the root of your project and Codex will read it automatically.

Use this option if you prefer project-local instructions instead of installing the skill globally.

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
API Version: V2 (April 2026 release)

For per-release deltas and the list of fields still in COMING SOON state, see [skills/sage-active-api/CHANGELOG.md](skills/sage-active-api/CHANGELOG.md).

## License

MIT
