# tokendrop — Cursor Integration Guide
# author: sudarshanpjadhav (Finggu) — finggu.com

## Method 1: Project Rules (Recommended)

Cursor supports `.mdc` rule files per project.

```
your-project/
└── .cursor/
    └── rules/
        ├── finggu-conventions.mdc
        ├── finggu-stack-node.mdc
        └── finggu-security.mdc
```

**Steps:**
1. Create `.cursor/rules/` folder in your project root
2. Copy any skill file from tokendrop → rename to `.mdc`
3. Cursor auto-applies matching rules based on file context

**Recommended rule combo per project type:**

| Project Type | Skills to Add |
|---|---|
| Node.js API | `finggu-conventions` + `finggu-stack-node` + `finggu-security` + `finggu-database-mysql` |
| WordPress Plugin | `finggu-conventions` + `finggu-wordpress` + `finggu-deployment` |
| React Frontend | `finggu-conventions` + `finggu-stack-react` + `finggu-css-framework` |
| Full SaaS | `finggu-master` (single file covers all) |
| PHP + cPanel | `finggu-conventions` + `finggu-stack-php` + `finggu-deployment` |

---

## Method 2: Global Rules

1. Open Cursor → Settings → General → Rules for AI
2. Paste `finggu-master.md` content here
3. Applies to every project automatically

---

## Method 3: @ Mention in Chat

```
@finggu-conventions.mdc write a login controller
@finggu-stack-node.mdc add rate limiting to this route
```

---

## `.mdc` File Format

Cursor `.mdc` files support frontmatter:

```markdown
---
description: Finggu naming conventions and code patterns
globs: ["**/*.js", "**/*.ts", "**/*.php"]
alwaysApply: true
---

[paste skill content here]
```

Set `alwaysApply: true` for conventions.
Set `globs` to restrict skill to specific file types.

---

## Token Tip

Use individual skills (not `finggu-master`) when working on a single-concern task.
`finggu-master` is best for new project scaffolding sessions.
