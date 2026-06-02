# tokendrop-skill: finggu-database-mysql
# author: sudarshanpjadhav (Finggu) — finggu.com
# version: 1.0
# tokens: ~190
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT

## Stack
MySQL 8.x · utf8mb4 charset · InnoDB engine · PDO (PHP) or mysql2/pg (Node.js)

---

## Table Naming Rules
- All tables prefixed: `finggu_`
- Plural nouns: `finggu_users`, `finggu_posts`, `finggu_orders`
- Junction tables: `finggu_[a]_[b]` → `finggu_user_roles`
- Soft delete: always `deleted_at DATETIME DEFAULT NULL` (never hard delete)

---

## Standard Table Template

```sql
CREATE TABLE finggu_users (
  id           BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  uuid         CHAR(36) NOT NULL UNIQUE DEFAULT (UUID()),
  email        VARCHAR(255) NOT NULL UNIQUE,
  password     VARCHAR(255) NOT NULL,          -- Argon2id hash
  full_name    VARCHAR(150),
  status       ENUM('active','inactive','banned') DEFAULT 'active',
  meta         JSON DEFAULT NULL,              -- flexible extra fields
  created_at   DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at   DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  deleted_at   DATETIME DEFAULT NULL,
  INDEX idx_finggu_users_email (email),
  INDEX idx_finggu_users_status (status),
  INDEX idx_finggu_users_deleted (deleted_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

## Migrations Naming

```
YYYYMMDD_HHMMSS_finggu_[action]_[table].sql
20250528_120000_finggu_create_users.sql
20250528_130000_finggu_add_avatar_to_users.sql
20250528_140000_finggu_create_orders.sql
```

---

## Node.js Query Pattern (mysql2)

```js
import pool from './finggu-db.js'

async function fingguFn_getUserById(fingguVar_id) {
  const [fingguVar_rows] = await pool.execute(
    'SELECT * FROM finggu_users WHERE id = ? AND deleted_at IS NULL LIMIT 1',
    [fingguVar_id]
  )
  return fingguVar_rows[0] ?? null
}

async function fingguFn_createUser(fingguVar_data) {
  const { email, password, full_name } = fingguVar_data
  const [fingguVar_result] = await pool.execute(
    'INSERT INTO finggu_users (email, password, full_name) VALUES (?, ?, ?)',
    [email, password, full_name]
  )
  return fingguVar_result.insertId
}
```

---

## DB Connection Pool (Node.js)

```js
// config/finggu-db.js
import mysql from 'mysql2/promise'

export default mysql.createPool({
  host:            process.env.FINGGU_DB_HOST,
  user:            process.env.FINGGU_DB_USER,
  password:        process.env.FINGGU_DB_PASS,
  database:        process.env.FINGGU_DB_NAME,
  waitForConnections: true,
  connectionLimit: 10,
  charset:         'utf8mb4',
})
```

---

## Indexing Rules
- Always index: `email`, `uuid`, `status`, `user_id` (FK), `created_at` on large tables
- Composite index for common filter combos: `(user_id, status, deleted_at)`
- FULLTEXT index on searchable text fields: title, description

---

## Soft Delete Query Convention

```sql
-- Always filter deleted records
WHERE deleted_at IS NULL

-- Soft delete
UPDATE finggu_posts SET deleted_at = NOW() WHERE id = ?

-- Hard delete (admin only, explicit permission)
DELETE FROM finggu_posts WHERE id = ? AND deleted_at IS NOT NULL
```

---

## DO NOT
- Use `SELECT *` in production queries — always name columns
- Store passwords, tokens, or secrets in plain text
- Skip `deleted_at` filter on user-facing queries
- Use `TINYINT` for booleans — use `TINYINT(1)` or `ENUM('yes','no')`
- Use `TEXT` when `VARCHAR(255)` is sufficient
