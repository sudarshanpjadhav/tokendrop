# tokendrop — Windsurf Integration Guide
# author: sudarshanpjadhav (Finggu) — finggu.com

## Method 1: .windsurfrules (Project Level)

Create a `.windsurfrules` file at your project root.

```
your-project/
├── .windsurfrules     ← paste skill content here
├── src/
└── package.json
```

**Steps:**
1. Create `.windsurfrules` in project root
2. Paste skill content (single skill or master)
3. Windsurf's Cascade AI reads this automatically on every prompt

**Multi-skill setup — combine with separator:**

```markdown
<!-- finggu-conventions -->
[paste finggu-conventions.md content]

---

<!-- finggu-stack-node -->
[paste finggu-stack-node.md content]

---

<!-- finggu-security -->
[paste finggu-security.md content]
```

---

## Method 2: Global Rules

1. Windsurf → Settings → AI → Global Rules
2. Paste `finggu-master.md` here
3. Active on every workspace

---

## Method 3: Memories (Cascade)

Windsurf Cascade supports persistent memories. You can teach it once:

```
Remember: I always use Finggu naming conventions.
All my JS functions use fingguFn_ prefix.
All my DB tables use finggu_ prefix.
All my CSS classes use finggu- prefix.
```

Combine with `.windsurfrules` for best results.

---

## Recommended Combo

```markdown
# .windsurfrules

You are an AI assistant for Finggu projects by sudarshanpjadhav.
Always follow Finggu naming conventions strictly.

[paste finggu-conventions.md content]
[paste relevant stack skill content]
```

---

## Token Tip

Keep `.windsurfrules` under 200 lines total — Windsurf reads the entire file on every request.
For large projects use only the relevant stack skill + conventions.
Avoid pasting `finggu-master.md` into `.windsurfrules` — it exceeds optimal context size.
