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
/plugin install sage-active-api@chkdskman-skill-sage-active-api
```

When you add a marketplace via GitHub shorthand (`owner/repo`), Claude Code references it as `owner-repo`. That's why the install command uses `@chkdskman-skill-sage-active-api`, not the `name` field declared inside `marketplace.json`.

After install, the skill triggers automatically when you mention "Sage Active", "sage api", "sage graphql", or any Sage Active entity name.

To pin a specific branch or tag, use the full git URL with `#<ref>` — the GitHub shorthand does not accept refs:

```text
/plugin marketplace add https://github.com/chkdskman/skill-sage-active-api.git#main
```

Local development / testing without installing — clone the repo and load it directly with `--plugin-dir`:

```bash
git clone https://github.com/chkdskman/skill-sage-active-api.git
claude --plugin-dir ./skill-sage-active-api
```

### Cursor (Skill)

Cursor 2.4 (January 2026) added native support for `SKILL.md`-based Agent Skills, the same format used by Claude Code. The skill in this repo works in Cursor without any modification — Cursor's agent picks it up automatically based on the skill's `description` frontmatter.

Skills can be installed at two scopes:

- **Project**: `.cursor/skills/<skill-name>/` — only available in this repo
- **Global**: `~/.cursor/skills/<skill-name>/` — available in every project on your machine

Pick one and install:

```bash
# Clone the repo somewhere (e.g. ~/src)
git clone https://github.com/chkdskman/skill-sage-active-api.git

# Project-scoped install
mkdir -p .cursor/skills
cp -r skill-sage-active-api/skills/sage-active-api .cursor/skills/sage-active-api

# OR global install (every project)
mkdir -p ~/.cursor/skills
cp -r skill-sage-active-api/skills/sage-active-api ~/.cursor/skills/sage-active-api
```

Then reload the Cursor workspace (Cmd/Ctrl+Shift+P → **Developer: Reload Window**). The skill activates on demand when you mention "Sage Active", "sage api", "sage graphql", or any Sage Active entity name. You can also invoke it explicitly by typing `/` in Agent chat.

> **On older Cursor versions (< 2.4):** native skills aren't supported. Use `.cursor/rules/*.mdc` Project Rules with the same skill files referenced as `@docs/...` paths instead. The legacy `.cursorrules` single-file format is deprecated — don't use it for new installs.

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
API Version: V2 (June 2026 release)

For per-release deltas and the list of fields still in COMING SOON state, see [skills/sage-active-api/CHANGELOG.md](skills/sage-active-api/CHANGELOG.md).

## License

MIT
