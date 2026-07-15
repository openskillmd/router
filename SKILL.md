---
name: openskill-router
description: Discovers and installs AI agent skills, collections, blueprints, and plugins from the OpenSkill registry. Use when an agent needs new capabilities, domain expertise, structured output templates, or runtime plugin bundles. Drives discovery through the osm CLI (install once with `npm i -g openskillmd`); falls back to the HTTP API when Node isn't available.
---

# OpenSkill Router

The skill router for AI agents. Discover, search, and install curated skills, collections, and blueprints from the OpenSkill registry — using the `osm` CLI.

**"I know Kung Fu."**

## Overview

OpenSkill.md is the App Store for AI agent capabilities. It has four layers:

1. **Skills** — Curated domain expertise files (`SKILL.md`) that teach agents how to perform specific tasks
2. **Collections** — Curated bundles of related skills, blueprints, and MCP servers. Some are domain buckets (`frontend`, `backend`, `ai-ml`); others are hand-picked sets (`the-anthropic-power-pack`, `the-mcp-builders-workshop`)
3. **Blueprints** — Self-correcting output specifications that guarantee structured results
4. **Plugins** — Plugin bundles (commands, agents, skills, hooks, MCP servers) that a runtime installs in one step from a marketplace

This router skill teaches your agent how to discover and use all four through the `osm` CLI.

## Setup — install the osm CLI

Discovery runs through the OpenSkill CLI. Ensure it's installed once (it's global):

```bash
npm i -g openskillmd
```

This exposes the `osm` command (aliases: `openskill`, `openskillmd`). Verify with `osm --version`.

> **Why the CLI and not raw downloads?** `osm add` resolves a skill, then writes it
> into the agent skill directories your project actually uses (e.g.
> `.claude/skills/<slug>/`), auto-detecting installed agents — so the skill is
> discoverable immediately. A plain file download lands wherever you run it and
> agents won't find it.

If `npm`/Node isn't available in this environment, use the **[HTTP API (fallback)](#http-api-fallback)** section below — every `osm` step has a `curl` equivalent.

## Discovery with osm

| What you need | Command |
|---|---|
| Search skills + blueprints by keyword | `osm search <query>` |
| Full details for a skill or blueprint | `osm info <slug>` |
| Install a skill (auto-placed for your agents) | `osm add <slug>` or `osm add <owner>/<repo>` — add `--json` for a machine-readable result |
| Install a specific skill from a multi-skill repo | `osm add <owner>/<repo>@<skill>` |
| Install a plugin (runs the native marketplace→install two-step under the hood) | `osm add <slug>` — project-scoped by default, `-g` for global |
| Browse collections interactively | `osm browse` |
| MCP server details / config | `osm mcp info <slug>` · `osm mcp setup` |

> CLI ≥ 0.3 resolves catalog slugs automatically: `osm add <slug>` looks the slug
> up in the registry and installs exactly that skill from its source repo (the
> `--json` result reports the substitution in `resolvedFrom`). Slugs that exist
> only in the registry with no public source fail with an `osm info <slug>`
> pointer. On CLI 0.2.x, resolve manually: `osm info <slug>` prints the
> `github.com/<owner>/<repo>` repository, then `osm add <owner>/<repo>`.

For **collections** and **registry stats** there's no non-interactive `osm`
command yet — use `osm browse` interactively, or the [HTTP API (fallback)](#http-api-fallback) for a scriptable list.

## How to Use

### When the agent needs a new capability

1. Search the registry for relevant skills:
   ```bash
   osm search testing
   ```
2. Inspect the best match (also reveals its `<owner>/<repo>` source):
   ```bash
   osm info webapp-testing
   ```
3. Install it by slug (lands in your agent's skill directory automatically;
   CLI ≥ 0.3 — on 0.2.x pass the `<owner>/<repo>` source from `osm info`):
   ```bash
   osm add webapp-testing
   ```
4. Apply the skill's instructions to the current task.

### When the agent needs a bundle of skills for a domain

1. List available collections (HTTP fallback — see below):
   ```bash
   curl "https://openskill.md/api/collections"
   ```
2. Open the relevant collection by its slug to see what it bundles:
   ```bash
   curl "https://openskill.md/api/collections/frontend"
   ```
   Each entry in the `skills` array carries `slug`, `githubOwner`, and `githubRepo`.
3. Install each skill by its catalog slug (CLI ≥ 0.3 resolves it and scopes the
   install to that one skill, even in multi-skill repos):
   ```bash
   osm add <slug>
   ```
   - On CLI 0.2.x, use the source coordinate instead: `osm add
     <githubOwner>/<githubRepo>`, disambiguating multi-skill repos with
     `osm add <githubOwner>/<githubRepo>@<skill>` (get `<skill>` from
     `osm info <slug>`).
   - If a skill has no public source at all (registry-only), `osm add <slug>`
     fails with an `osm info <slug>` pointer — inspect it there.

### When the agent needs structured output

1. Search blueprints for the output type:
   ```bash
   osm search report
   ```
2. Read the blueprint spec with `osm info <slug>` and follow its structure for the output.

### When the agent needs a plugin

Plugins bundle commands, agents, skills, hooks, and MCP servers that a runtime
(Claude Code, Codex) installs in one step from a marketplace.

1. Search the registry for a relevant plugin:
   ```bash
   osm search <query>
   ```
2. Inspect the best match — its runtimes, marketplaces, and the install string:
   ```bash
   osm info <slug>
   ```
3. Install it (`osm add` runs the native marketplace→install two-step for you;
   project-scoped by default, add `-g` for a global install):
   ```bash
   osm add <slug>
   ```
4. Restart / reload the runtime so it picks up the plugin's commands and agents.

## Presenting Results

When recommending skills to a user, go beyond listing names and descriptions. Contextualize each recommendation to the user's current task.

### Contextualize recommendations

Explain *why* a skill is relevant. Instead of "Here's a skill called `webapp-testing`", say "Since you're building a React app and need end-to-end tests, the `webapp-testing` skill covers Playwright setup, test patterns, and CI integration."

### Show key quality signals

For each recommended skill, surface:

- **Score** — the registry quality score (higher is better)
- **Install count** — how many agents have used it
- **Verified author** — whether the author is verified; mention this as a trust signal

Example format:
```
webapp-testing (score: 92 · 1.4k installs · ✓ verified)
  End-to-end testing with Playwright — covers setup, selectors, and CI.
```

### Suggest collections for broad needs

When a user's request spans an entire domain rather than a single task (e.g. "help me build a frontend"), recommend the relevant collection instead of listing individual skills. Mention what the collection includes so the user understands the breadth:

> Your project covers React components, styling, and accessibility — the `frontend` collection bundles skills for all of these.

### Comparing Multiple Results

When several skills match a search, present a concise comparison rather than dumping a raw list:

1. **Rank by score** — list highest-scoring skills first.
2. **Note verified authors** — mark verified authors with ✓ so the user can weigh trust.
3. **Highlight differentiators** — briefly note what makes each skill unique (e.g. one focuses on unit tests, another on integration tests).
4. **Cap the list** — show the top 3–5 results. If more exist, mention the total and offer to show more.

Example comparison:

```
Found 7 skills for "testing". Top 3:

1. webapp-testing (score: 92 · 1.4k installs · ✓ verified)
   Full E2E testing with Playwright — setup, fixtures, CI integration.

2. unit-test-patterns (score: 87 · 980 installs · ✓ verified)
   Unit testing best practices — mocking, coverage thresholds, TDD workflow.

3. api-testing (score: 81 · 620 installs)
   REST & GraphQL API test suites — request builders, assertions, auth flows.

4 more results available. Want me to show them?
```

## Smart Fallbacks

When a search doesn't return a perfect match, work through these tiers before telling the user nothing was found. Each tier broadens the net.

### Tier 1 — Search skills directly

```bash
osm search <query>
```

If results come back with relevant matches, present them using the guidelines above. If no results or results are clearly irrelevant, move to Tier 2.

### Tier 2 — Check collections for the domain

```bash
curl "https://openskill.md/api/collections"
```

Scan the collection list for one that covers the user's domain. A collection may contain a relevant skill even if the direct search missed it because the search term didn't match the skill's name or description exactly. If a matching collection is found, open it (`/api/collections/<slug>`) and suggest its skills. If not, move to Tier 3.

### Tier 3 — Search blueprints for structured output

```bash
osm search <query>
```

`osm search` returns blueprints alongside skills. Even when no skill exists for a task, a blueprint might provide the structured output format the user needs. If a relevant blueprint is found, recommend it and explain how its structured spec can guide the work.

### Tier 4 — Offer to help directly

Only after exhausting all three layers above, tell the user:

> I couldn't find a skill, collection, or blueprint for that in the OpenSkill registry. I can still help you with this directly — want me to proceed with my built-in knowledge?

Never jump straight to "not found." Always work through the tiers first.

## HTTP API (fallback)

When Node/`npm` isn't available — or you need raw JSON, collections, or registry stats — hit the HTTP API directly.

Base URL: `https://openskill.md`

### Search Skills

```bash
curl "https://openskill.md/api/skills?search=react&limit=10"
```

Response: `{ data, pagination }`. Each item in `data` has `name`, `slug`, `description`, `author`, `installCount`, `subcategory`, `ownerVerified`, `ownerAvatarUrl`.

### Get Skill Detail

```bash
curl "https://openskill.md/api/skills/{slug}"
```

Response: Full skill object including `content` (the SKILL.md body), `sourceUrl`, `license`, `githubOwner`, `githubRepo`. Use `githubOwner`/`githubRepo` to build the `osm add <owner>/<repo>` command.

### List Collections

```bash
curl "https://openskill.md/api/collections"
```

Response: `{ data, pagination }`. Each item in `data` has `name`, `slug`, `description`, `icon`, and item counts (`skillCount`, `blueprintCount`, `mcpServerCount`). Use the `slug` (e.g. `frontend`, `the-anthropic-power-pack`) — not the display `name` — for the detail call below.

**The list is paginated** — `pagination` is `{ page, limit, total, totalPages }`, and a single call returns only the first page (there are 100+ collections). To get the complete set, pass `?limit=` / `?page=` and keep fetching until `page === totalPages`:

```bash
curl "https://openskill.md/api/collections?page=1&limit=100"
```

```bash
curl "https://openskill.md/api/collections/{slug}"
```

Response: One collection with `skills`, `blueprints`, and `mcpServers` arrays. Each skill carries `slug`, `githubOwner`, and `githubRepo` — the coordinates needed for `osm add <githubOwner>/<githubRepo>`.

Example collection slugs: `frontend`, `backend`, `fullstack`, `ai-ml`, `devops`, `design`, `the-anthropic-power-pack`, `the-mcp-builders-workshop`, `build-with-nextjs`. Run the list endpoint for the current, complete set.

### Search / Get Blueprints

```bash
curl "https://openskill.md/api/blueprints?search=invoice&limit=10"
curl "https://openskill.md/api/blueprints/{slug}"
```

Response: Blueprint objects with `name`, `slug`, `description`, `category`, `difficulty`, and (on detail) `content` (the structured output spec).

### Registry Stats

```bash
curl "https://openskill.md/api/stats"
```

Response: `{ skills, blueprints, collections, mcpServers, plugins, totalDownloads }`.

### MCP Servers

The `osm mcp info` / `osm mcp setup` equivalents over HTTP:

```bash
curl "https://openskill.md/api/mcp-servers?search=postgres&limit=10"   # list & search
curl "https://openskill.md/api/mcp-servers/{slug}"                     # server detail
```

To connect an MCP client directly, point it at `https://openskill.md/api/mcp` and set
`"type": "http"` — that key is what selects Streamable HTTP (MCP spec rev 2025-03-26).
Omit it and most clients fall back to the deprecated SSE transport.

```json
{
  "mcpServers": {
    "openskill": {
      "type": "http",
      "url": "https://openskill.md/api/mcp"
    }
  }
}
```

The older SSE endpoint `https://openskill.md/api/mcp/sse` still responds, so existing
configs keep working — but new ones should use the above.

### Plugins

The `osm search` / `osm info` / `osm add <slug>` equivalents over HTTP:

```bash
curl "https://openskill.md/api/plugins?search=postgres&limit=10"   # list & search
curl "https://openskill.md/api/plugins/{slug}"                     # plugin detail
```

The detail response returns `installCommand` (the native marketplace→install
two-step string the runtime runs), `runtimes` (which agents support it, e.g.
`claude-code`, `codex`), and `marketplaces` (where the bundle is published).

### Manual install (no CLI)

Download a skill's `SKILL.md` straight into your agent's skill directory so it stays discoverable:

```bash
mkdir -p .claude/skills/<slug>
curl -sL "https://openskill.md/api/skills/<slug>" | jq -r '.content' \
  > .claude/skills/<slug>/SKILL.md
```

## Skill File Format

Skills follow the OpenSkill standard — a `SKILL.md` file with YAML frontmatter:

```markdown
---
name: my-skill
description: What this skill does and when to use it.
---

# Skill Title

Instructions, rules, templates, and examples that teach the agent
how to perform this specific task.
```

## Quick Start

```bash
npm i -g openskillmd          # one-time CLI install
osm search <what you need>    # discover skills + blueprints
osm add <owner>/<repo>        # install — auto-placed where your agent looks
```

Browse the full registry: [https://openskill.md](https://openskill.md)
