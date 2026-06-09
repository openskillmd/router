# OpenSkill Router

The skill router for AI agents. One install gives your agent access to the entire [OpenSkill](https://openskill.md) registry — skills, skill-sets, and blueprints.

## Install

### Via the osm CLI (recommended — installs to all detected agents automatically)

```bash
npm i -g openskillmd
osm add openskillmd/router
```

`osm add` writes the router into your project's agent skill directories (e.g.
`.claude/skills/`), auto-detecting installed agents, so it's discoverable right away.
The CLI exposes the `osm` command (aliases: `openskill`, `openskillmd`).

### Claude Code (manual)

```bash
mkdir -p .claude/skills/openskill-router
curl -sL https://raw.githubusercontent.com/openskillmd/router/main/SKILL.md \
  > .claude/skills/openskill-router/SKILL.md
```

### Universal Agents — Cursor, Codex, Cline, Gemini CLI, GitHub Copilot, Windsurf, and others (manual)

```bash
mkdir -p .agents/skills/openskill-router
curl -sL https://raw.githubusercontent.com/openskillmd/router/main/SKILL.md \
  > .agents/skills/openskill-router/SKILL.md
```

## What It Does

Once installed, your AI agent discovers capabilities through the `osm` CLI:

- **Search skills + blueprints** — `osm search react`
- **Inspect a match** — `osm info <slug>`
- **Install (auto-placed for your agents)** — `osm add <owner>/<repo>`
- **MCP servers** — `osm mcp info <slug>` · `osm mcp setup`

Skill-sets (19 domain bundles like `@frontend`, `@backend`, `@ai-ml`) and registry
stats are available over the [HTTP API](https://openskill.md) as a fallback when the
CLI isn't an option.

## Registry

- **3,300+ skills** from 80+ official publishers (Microsoft, Anthropic, Vercel, Supabase, Stripe, and more)
- **19 skill-sets** — domain bundles like `@frontend`, `@backend`, `@devops`, `@ai-ml`
- **130+ blueprints** — self-correcting output specs (invoices, resumes, pitch decks, API docs)

## Links

- [OpenSkill.md](https://openskill.md) — Browse the registry
- [API Reference](https://openskill.md/router) — Full API documentation
- [Install Guide](https://openskill.md/install) — Platform-specific setup

## License

MIT
