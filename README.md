# OpenSkill Router

The skill router for AI agents. One install gives your agent access to the entire [OpenSkill](https://openskillmd.com) registry — skills, skill-sets, and blueprints.

## Install

### Via npx (Recommended — installs to all detected agents automatically)

```bash
npx skills add openskillmd/router
```

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

Once installed, your AI agent can:

- **Search skills** — `curl "https://openskillmd.com/api/skills?search=react"`
- **Browse skill-sets** — `curl "https://openskillmd.com/api/skillsets"` (19 domain bundles like `@frontend`, `@backend`, `@ai-ml`)
- **Find blueprints** — `curl "https://openskillmd.com/api/blueprints?search=invoice"` (structured output specs)
- **Check registry stats** — `curl "https://openskillmd.com/api/stats"`

## Registry

- **3,300+ skills** from 80+ official publishers (Microsoft, Anthropic, Vercel, Supabase, Stripe, and more)
- **19 skill-sets** — domain bundles like `@frontend`, `@backend`, `@devops`, `@ai-ml`
- **130+ blueprints** — self-correcting output specs (invoices, resumes, pitch decks, API docs)

## Links

- [OpenSkill.md](https://openskillmd.com) — Browse the registry
- [API Reference](https://openskillmd.com/router) — Full API documentation
- [Install Guide](https://openskillmd.com/install) — Platform-specific setup

## License

MIT
