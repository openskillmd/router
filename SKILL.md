---
name: openskill-router
description: Discovers and installs AI agent skills, skill-sets, and blueprints from the OpenSkill registry. Use when an agent needs new capabilities, domain expertise, or structured output templates. One curl command to find everything.
---

# OpenSkill Router

The skill router for AI agents. Discover, search, and install curated skills, skill-sets, and blueprints from the OpenSkill registry.

**"I know Kung Fu."**

## Overview

OpenSkill.md is the App Store for AI agent capabilities. It has three layers:

1. **Skills** — Curated domain expertise files (`SKILL.md`) that teach agents how to perform specific tasks
2. **Skill-Sets** — Bundles of related skills grouped by domain (e.g. `@frontend`, `@backend`, `@ai-ml`)
3. **Blueprints** — Self-correcting output specifications that guarantee structured results

This router skill teaches your agent how to discover and use all three.

## API Reference

Base URL: `https://openskillmd.com`

### Search Skills

```bash
curl "https://openskillmd.com/api/skills?search=react&limit=10"
```

Response: Array of skill objects with `name`, `slug`, `description`, `author`, `installCount`, `subcategory`, `ownerVerified`, `ownerAvatarUrl`.

### Get Skill Detail

```bash
curl "https://openskillmd.com/api/skills/{slug}"
```

Response: Full skill object including `content` (the SKILL.md body), `sourceUrl`, `license`, `githubOwner`, `githubRepo`.

### List Skill-Sets

```bash
curl "https://openskillmd.com/api/skillsets"
```

Response: Array of skill-set objects. Each has `name`, `slug`, `description`, `icon`, and a `skills` array of included skill summaries.

Available skill-sets:
- `@frontend` — React, Vue, CSS, design systems
- `@backend` — APIs, databases, server patterns
- `@fullstack` — End-to-end web development
- `@mobile` — React Native, Expo, mobile UX
- `@devops` — CI/CD, Docker, infrastructure
- `@ai-ml` — Machine learning, LLMs, embeddings
- `@security` — Auth, encryption, vulnerability scanning
- `@design` — UI/UX, branding, accessibility
- `@media` — Video, audio, image processing
- `@productivity` — Automation, workflows, documentation

### Search Blueprints

```bash
curl "https://openskillmd.com/api/blueprints?search=invoice&limit=10"
```

Response: Array of blueprint objects with `name`, `slug`, `description`, `category`, `difficulty`.

### Get Blueprint Detail

```bash
curl "https://openskillmd.com/api/blueprints/{slug}"
```

Response: Full blueprint object including `content` (the structured output spec).

### Registry Stats

```bash
curl "https://openskillmd.com/api/stats"
```

Response: `{ skills, blueprints, skillsets, totalDownloads }`.

## How to Use

### When the agent needs a new capability

1. Search the registry for relevant skills:
   ```bash
   curl "https://openskillmd.com/api/skills?search=testing"
   ```
2. Read the full skill content:
   ```bash
   curl "https://openskillmd.com/api/skills/webapp-testing"
   ```
3. Apply the skill's instructions to the current task.

### When the agent needs a bundle of skills for a domain

1. List available skill-sets:
   ```bash
   curl "https://openskillmd.com/api/skillsets"
   ```
2. Pick the relevant set (e.g. `@frontend`) and load each skill's content.

### When the agent needs structured output

1. Search blueprints for the output type:
   ```bash
   curl "https://openskillmd.com/api/blueprints?search=report"
   ```
2. Read the blueprint spec and follow its structure for the output.

## Presenting Results

When recommending skills to a user, go beyond listing names and descriptions. Contextualize each recommendation to the user's current task.

### Contextualize recommendations

Explain *why* a skill is relevant. Instead of "Here's a skill called `webapp-testing`", say "Since you're building a React app and need end-to-end tests, the `webapp-testing` skill covers Playwright setup, test patterns, and CI integration."

### Show key quality signals

For each recommended skill, surface:

- **Score** — the registry quality score (higher is better)
- **Install count** — how many agents have used it
- **Verified author** — whether `ownerVerified` is `true`; mention this as a trust signal

Example format:
```
webapp-testing (score: 92 · 1.4k installs · ✓ verified)
  End-to-end testing with Playwright — covers setup, selectors, and CI.
```

### Suggest skill-sets for broad needs

When a user's request spans an entire domain rather than a single task (e.g. "help me build a frontend"), recommend the relevant skill-set instead of listing individual skills. Mention what the set includes so the user understands the breadth:

> Your project covers React components, styling, and accessibility — the `@frontend` skill-set bundles all of these together.

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
curl "https://openskillmd.com/api/skills?search={query}&limit=5"
```

If results come back with relevant matches, present them using the guidelines above. If no results or results are clearly irrelevant, move to Tier 2.

### Tier 2 — Check skill-sets for the domain

```bash
curl "https://openskillmd.com/api/skillsets"
```

Scan the skill-set list for a set that covers the user's domain. A skill-set may contain a relevant skill even if the direct search missed it because the search term didn't match the skill's name or description exactly. If a matching set is found, suggest it. If not, move to Tier 3.

### Tier 3 — Search blueprints for structured output

```bash
curl "https://openskillmd.com/api/blueprints?search={query}&limit=5"
```

Even when no skill exists for a task, a blueprint might provide the structured output format the user needs. If a relevant blueprint is found, recommend it and explain how its structured spec can guide the work.

### Tier 4 — Offer to help directly

Only after exhausting all three layers above, tell the user:

> I couldn't find a skill, skill-set, or blueprint for that in the OpenSkill registry. I can still help you with this directly — want me to proceed with my built-in knowledge?

Never jump straight to "not found." Always work through the tiers first.

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

One command to discover everything:

```bash
curl "https://openskillmd.com/api/stats"
```

Browse the full registry: [https://openskillmd.com](https://openskillmd.com)
