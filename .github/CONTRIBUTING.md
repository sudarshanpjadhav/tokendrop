# Contributing to tokendrop

> Thank you for helping make AI development more token-efficient.

---

## Before You Start

1. Check existing skills — don't duplicate what's already there
2. Each skill must solve a real token-waste problem
3. Test your skill in at least one AI editor before submitting

---

## Skill File Rules (Non-Negotiable)

| Rule | Requirement |
|---|---|
| Max lines | ≤ 150 lines |
| Header | Must include `# tokendrop-skill:`, `# author:`, `# version:`, `# tokens:` |
| Token estimate | Count using [tokenizer](https://platform.openai.com/tokenizer) — must be accurate |
| Finggu naming | All code examples use `fingguFn_`, `fingguVar_`, `FINGGU_`, `finggu-`, `finggu_` |
| No filler | No "Introduction", "Overview", "In summary" sections |
| Tested | Must be verified in Cursor, Windsurf, Copilot, Continue, or Claude |

---

## Skill File Header Template

```markdown
# tokendrop-skill: [category]-[name]
# author: [your-github-handle] (via Finggu)
# version: 1.0
# tokens: ~[NNN]
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT
```

---

## File Location

```
skills/
├── conventions/    ← naming, code style, project structure
├── stack/          ← language/framework-specific patterns
├── database/       ← DB schema, query patterns, migrations
├── saas/           ← billing, multi-tenancy, subscriptions
├── wordpress/      ← WP plugin, theme, hooks
├── security/       ← auth, hashing, headers, validation
├── deployment/     ← hosting, CI/CD, server config
└── css-framework/  ← CSS conventions, design tokens
```

---

## Pull Request Checklist

- [ ] Skill file is under 150 lines
- [ ] Header complete with token count
- [ ] All code examples use Finggu prefixes
- [ ] No duplicate of existing skill
- [ ] Tested in at least one editor
- [ ] PR title: `feat(skills): add [category]/[skill-name]`
- [ ] PR description explains what token problem this skill solves

---

## Skill Quality Bar

**Good skill** — Replaces repetitive context you'd paste on every session:
> "Every time I start a PHP project I explain PDO connection pooling, prepared statements, and file structure. This skill covers all of it in 40 lines."

**Bad skill** — Generic documentation that's already in model training data:
> "PHP is a server-side language. Arrays are ordered maps..."

---

## Questions?

Open a GitHub Discussion or reach out via [finggu.com](https://finggu.com)

---

*tokendrop is maintained by [sudarshanpjadhav](https://github.com/sudarshanpjadhav) · Finggu*
