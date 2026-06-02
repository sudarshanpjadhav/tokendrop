# tokendrop — GitHub Copilot Integration Guide
# author: sudarshanpjadhav (Finggu) — finggu.com

## Method: copilot-instructions.md

```
your-project/
└── .github/
    └── copilot-instructions.md    ← paste skill content here
```

**Steps:**
1. Create `.github/copilot-instructions.md`
2. Paste relevant skill content
3. GitHub Copilot reads this as project context automatically (VS Code + Copilot extension)

**Recommended content for copilot-instructions.md:**

```markdown
# Finggu Project Instructions

This project follows Finggu conventions by sudarshanpjadhav (finggu.com).

## Naming Rules
- JS functions: `fingguFn_` prefix
- JS variables: `fingguVar_` prefix
- Constants: `FINGGU_` prefix
- CSS classes: `finggu-` prefix
- DB tables: `finggu_` prefix
- Redis keys: `finggu:` prefix

[paste one or two relevant stack skills below]
```

---

## Token Tip

Copilot reads `copilot-instructions.md` on every suggestion.
Keep it under 150 lines. Use individual skills, not `finggu-master`.

---
---

# tokendrop — Continue.dev Integration Guide
# author: sudarshanpjadhav (Finggu) — finggu.com

## Method 1: Context Provider

Add tokendrop skills as file context providers in `.continue/config.json`:

```json
{
  "contextProviders": [
    {
      "name": "file",
      "params": {
        "path": ".tokendrop/finggu-conventions.md"
      }
    },
    {
      "name": "file",
      "params": {
        "path": ".tokendrop/finggu-stack-node.md"
      }
    }
  ]
}
```

**Folder setup:**
```
your-project/
└── .tokendrop/
    ├── finggu-conventions.md
    ├── finggu-stack-node.md
    └── finggu-security.md
```

---

## Method 2: System Prompt

In `.continue/config.json`:

```json
{
  "models": [
    {
      "title": "Finggu Dev",
      "provider": "anthropic",
      "model": "claude-sonnet-4-20250514",
      "systemMessage": "[paste finggu-master.md content here]"
    }
  ]
}
```

---

## Method 3: @ mention in chat

```
@finggu-conventions.md refactor this function
@finggu-stack-php.md add a new DB query function
```

---

## Token Tip

Continue.dev sends context providers on every request.
Use `.tokendrop/` folder with only the skills relevant to the current module you're working on.
Swap files in/out as you move between frontend and backend work.
