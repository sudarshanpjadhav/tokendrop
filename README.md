# 🪂 tokendrop

> **Drop tokens. Not quality.**

A battle-tested collection of AI skill files for developers — plug them into any AI editor and instantly reduce token usage while getting more precise, context-aware responses.

Built and maintained by **[Finggu](https://finggu.com)** · [`sudarshanpjadhav`](https://github.com/sudarshanpjadhav)

---

## What is tokendrop?

Every time you open a new AI chat or session, your model wastes tokens re-learning:
- Your stack
- Your naming conventions
- Your deployment environment
- Your security patterns

**tokendrop** gives you pre-written, ultra-compact skill files that frontload that context in ≤ 150 lines — so every prompt you send goes straight to work, not warm-up.

---

## Skills Available

| Skill File | What It Covers | Token Save |
|---|---|---|
| [`conventions/finggu-conventions`](./skills/conventions/finggu-conventions.md) | Naming rules, prefixes, file structure | ~200 tokens/session |
| [`stack/finggu-stack-node`](./skills/stack/finggu-stack-node.md) | Node.js + Fastify + Express patterns | ~300 tokens/session |
| [`stack/finggu-stack-php`](./skills/stack/finggu-stack-php.md) | PHP + cPanel shared hosting patterns | ~250 tokens/session |
| [`stack/finggu-stack-react`](./skills/stack/finggu-stack-react.md) | React 19 + Vite component patterns | ~200 tokens/session |
| [`database/finggu-database-mysql`](./skills/database/finggu-database-mysql.md) | MySQL schema, queries, indexing | ~200 tokens/session |
| [`saas/finggu-saas`](./skills/saas/finggu-saas.md) | Multi-tenant, billing, Razorpay | ~350 tokens/session |
| [`wordpress/finggu-wordpress`](./skills/wordpress/finggu-wordpress.md) | WP plugin anatomy, hooks, prefixes | ~300 tokens/session |
| [`security/finggu-security`](./skills/security/finggu-security.md) | JWT, Argon2id, rate limiting, OWASP | ~250 tokens/session |
| [`deployment/finggu-deployment`](./skills/deployment/finggu-deployment.md) | cPanel, shared hosting, Render, VPS | ~200 tokens/session |
| [`css-framework/finggu-css`](./skills/css-framework/finggu-css.md) | CSS framework conventions, Noxen | ~150 tokens/session |
| [`master/finggu-master`](./master/finggu-master.md) | All-in-one ultra-compact skill | ~500 tokens/session |

---

## Quick Start

### Cursor
```
Copy any skill file content → paste into .cursor/rules/[skill-name].mdc
```

### Windsurf
```
Copy any skill file content → paste into .windsurfrules at project root
```

### GitHub Copilot
```
Copy any skill file content → paste into .github/copilot-instructions.md
```

### Continue.dev
```json
{
  "contextProviders": [
    { "name": "file", "params": { "path": ".tokendrop/finggu-master.md" } }
  ]
}
```

### Claude / ChatGPT / Any Chat UI
```
Paste skill file content at the top of your first message or as system context.
```

---

## File Format

Every skill file follows this contract:

```
# tokendrop-skill: [skill-name]
# author: sudarshanpjadhav (Finggu)
# version: x.x
# tokens: ~NNN
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT
```

- ✅ ≤ 150 lines (hard ceiling)
- ✅ No filler words
- ✅ Machine-readable structure
- ✅ Finggu naming conventions throughout
- ✅ Real-world patterns only

---

## Contributing

Read [`CONTRIBUTING.md`](.github/CONTRIBUTING.md) before submitting a PR.

Rules:
1. Every skill must stay ≤ 150 lines
2. Must include token estimate in header
3. Must use Finggu prefix conventions in all code examples
4. Tested in at least one AI editor before PR

---

## License

MIT — use freely in any project, commercial or personal.

---

<p align="center">
  Made with ☕ in Mumbai by <a href="https://finggu.com">Finggu</a>
</p>
