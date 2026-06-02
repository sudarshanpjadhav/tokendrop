# tokendrop-skill: finggu-master
# author: sudarshanpjadhav (Finggu) — finggu.com
# version: 1.0
# tokens: ~420
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT
# note: Use this file when you need full Finggu context in a single paste.
#       For token-constrained editors, use individual skills from /skills/ instead.

---

## Identity
Brand: Finggu | finggu.com
Author: sudarshanpjadhav | Mumbai, India
Stack: PHP + Node.js (Fastify/Express) + React 19 + MySQL/PostgreSQL + Redis + cPanel

---

## Naming Prefixes — STRICT

| Context | Prefix | Example |
|---|---|---|
| CSS classes | `finggu-` | `finggu-card` |
| JS functions | `fingguFn_` | `fingguFn_getUser()` |
| JS variables | `fingguVar_` | `fingguVar_token` |
| Constants | `FINGGU_` | `FINGGU_API_URL` |
| PHP functions | `fingguFn_` | `fingguFn_sanitize()` |
| DB tables | `finggu_` | `finggu_users` |
| Redis keys | `finggu:` | `finggu:session:123` |
| Env vars | `FINGGU_` | `FINGGU_DB_HOST` |
| WP hooks/options | `finggu_` | `finggu_init` |
| Internal npm | `@finggu/` | `@finggu/auth` |
| CSS custom props | `--finggu-` | `--finggu-primary` |

---

## File Header (Every File)

JS/TS:
```js
/** @package Finggu | @author sudarshanpjadhav | @link finggu.com */
```
PHP:
```php
<?php /** @package Finggu | @author sudarshanpjadhav | @since 1.0.0 */ defined('FINGGU_ROOT') || exit;
```

---

## Node.js Patterns

```js
// Controller — always async/await, structured errors
export async function fingguFn_handleRequest(req, reply) {
  const fingguVar_parsed = fingguVar_schema.safeParse(req.body)
  if (!fingguVar_parsed.success) return reply.code(422).send({ error: 'FINGGU_ERR_VALIDATION' })
  try {
    const fingguVar_result = await fingguFn_serviceMethod(fingguVar_parsed.data)
    return reply.send({ success: true, data: fingguVar_result })
  } catch (fingguVar_err) {
    req.log.error(fingguVar_err)
    return reply.code(500).send({ error: 'FINGGU_ERR_SERVER_001' })
  }
}
```

Response format always:
```js
{ success: true,  data: {},  meta: {} }       // success
{ success: false, error: 'FINGGU_ERR_[X]_[N]' }  // error
```

---

## PHP Patterns

```php
// DB — always PDO prepared statements
function fingguFn_query(string $fingguVar_sql, array $fingguVar_params = []): array {
  $fingguVar_stmt = fingguFn_getDB()->prepare($fingguVar_sql);
  $fingguVar_stmt->execute($fingguVar_params);
  return $fingguVar_stmt->fetchAll();
}

// JSON response
function fingguFn_json(bool $ok, mixed $data, int $code = 200): void {
  http_response_code($code);
  header('Content-Type: application/json');
  echo json_encode(['success' => $ok, 'data' => $data]);
  exit;
}
```

---

## React Patterns

```jsx
// Component — props prefixed fingguProp_
export default function FingguCard({ fingguProp_title, fingguProp_children }) {
  const [fingguVar_open, setOpen] = useState(false)
  return <div className="finggu-card">{fingguProp_title}</div>
}

// Hook
export function useFingguAuth() {
  const fingguVar_user = useFingguStore(s => s.user)
  return { fingguVar_user }
}

// API service — all calls through finggu-api.js layer
export const fingguFn_getUser = (id) => fingguApi.get(`/users/${id}`)
```

---

## MySQL Table Template

```sql
CREATE TABLE finggu_[name] (
  id         BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  uuid       CHAR(36) NOT NULL UNIQUE DEFAULT (UUID()),
  tenant_id  BIGINT UNSIGNED,                      -- multi-tenant
  status     ENUM('active','inactive') DEFAULT 'active',
  meta       JSON DEFAULT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  deleted_at DATETIME DEFAULT NULL,
  INDEX idx_finggu_[name]_tenant (tenant_id),
  INDEX idx_finggu_[name]_status (status)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

## Security Baseline
- Passwords: **Argon2id** only (memoryCost: 65536, timeCost: 3)
- Auth tokens: **JWT RS256**, 15min access + 7d refresh rotation
- Input: **Zod** validation before service layer
- Rate limit: 10 req/15min on auth, 60 req/min on API
- Headers: **Helmet.js** + CSP
- SQL: **always parameterized** — never string concatenation
- PHP: **always prepared PDO** — never `mysql_*`

---

## WordPress Conventions
- Plugin prefix: `finggu_` on all hooks, options, DB tables, AJAX actions
- Main class: singleton pattern with `fingguFn_getInstance()`
- AJAX: always `check_ajax_referer('finggu_nonce', 'nonce')` first
- Assets: `wp_enqueue_*` with version `FINGGU_PLUGIN_VERSION`
- `wp_localize_script` object name: `fingguPluginVars`

---

## Deployment
- Frontend/PHP → cPanel shared hosting
- Node.js → cPanel Node.js App Manager or Render.com
- `.env` → never committed, `600` permissions
- Uploads → outside `public_html`, served via proxy
- `.htaccess` → `Options -Indexes`, `display_errors off`
- Pre-deploy: remove all `console.log` / `var_dump`, set `FINGGU_ENV=production`

---

## Razorpay (India)
- Amounts in **paise** (×100)
- Verify webhook with `x-razorpay-signature` header HMAC-SHA256
- Store `razorpay_order_id` before payment redirect
- Webhook endpoint: skip auth middleware, signature validates instead

---

## Error Code Format
`FINGGU_ERR_[MODULE]_[NNN]`
Examples: `FINGGU_ERR_AUTH_001`, `FINGGU_ERR_DB_001`, `FINGGU_ERR_TENANT_002`
