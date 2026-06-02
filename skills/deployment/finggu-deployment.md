# tokendrop-skill: finggu-deployment
# author: sudarshanpjadhav (Finggu) — finggu.com
# version: 1.0
# tokens: ~190
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT

## Environments
- **Frontend/PHP**: cPanel shared hosting (primary)
- **Node.js backend**: cPanel Node.js App Manager OR Render.com free/paid tier
- **DB**: cPanel MySQL (shared) or PlanetScale/Supabase (managed)
- **Redis**: Upstash (serverless, free tier friendly)

---

## cPanel Shared Hosting — Rules

```
public_html/           ← web root, only public assets here
  index.php
  assets/
  .htaccess

private/               ← outside public_html
  config/
  uploads/
  logs/
```

**.htaccess baseline:**
```apache
Options -Indexes
ServerSignature Off
php_flag display_errors off
php_value upload_max_filesize 10M
php_value post_max_size 12M

# Block direct access to sensitive files
<FilesMatch "\.(env|log|json|md|sql|sh)$">
  Order allow,deny
  Deny from all
</FilesMatch>

# Pretty URLs
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php?url=$1 [QSA,L]
```

---

## Node.js on cPanel (App Manager)

```
App root:        /home/[cpanel-user]/finggu-[app]/
Startup file:    server.js
Node version:    20.x LTS
Application URL: /api  (proxied from public_html)
```

```js
// server.js — always bind 0.0.0.0, port from ENV
const FINGGU_PORT = parseInt(process.env.PORT || process.env.FINGGU_PORT || 3000)
app.listen(FINGGU_PORT, '0.0.0.0', () => {
  console.log(`[Finggu] Server running on port ${FINGGU_PORT}`)
})
```

---

## Render.com Deployment

```yaml
# render.yaml
services:
  - type: web
    name: finggu-[app]-api
    env: node
    buildCommand: npm install
    startCommand: node server.js
    envVars:
      - key: FINGGU_ENV
        value: production
      - key: FINGGU_DB_URL
        sync: false
      - key: FINGGU_JWT_PRIVATE_KEY
        sync: false
```

**Free tier limits to know:**
- Spins down after 15min inactivity — add a keepalive ping if needed
- 512MB RAM — avoid in-memory caching of large datasets
- No persistent disk — use external storage (S3, Cloudinary)

---

## .env File Convention

```bash
# finggu-[project]/.env
FINGGU_ENV=production
FINGGU_PORT=3000
FINGGU_APP_URL=https://finggu.com

# Database
FINGGU_DB_HOST=localhost
FINGGU_DB_PORT=3306
FINGGU_DB_NAME=finggu_[project]
FINGGU_DB_USER=
FINGGU_DB_PASS=

# Auth
FINGGU_JWT_PRIVATE_KEY_PATH=./certs/private.pem
FINGGU_JWT_PUBLIC_KEY_PATH=./certs/public.pem

# External APIs
FINGGU_RAZORPAY_KEY_ID=
FINGGU_RAZORPAY_KEY_SECRET=
FINGGU_REDIS_URL=
```

---

## File Upload Handling (cPanel)

```php
// Store uploads outside public_html
define('FINGGU_UPLOAD_DIR', dirname($_SERVER['DOCUMENT_ROOT']) . '/private/uploads/');
define('FINGGU_UPLOAD_URL', 'https://finggu.com/file-proxy.php?f=');  // serve via proxy

function fingguFn_handleUpload(array $fingguVar_file): string {
  $fingguVar_allowed = ['image/jpeg', 'image/png', 'image/webp', 'application/pdf'];
  if (!in_array(mime_content_type($fingguVar_file['tmp_name']), $fingguVar_allowed)) {
    throw new Exception('FINGGU_ERR_UPLOAD_001');
  }
  $fingguVar_ext      = pathinfo($fingguVar_file['name'], PATHINFO_EXTENSION);
  $fingguVar_filename = 'finggu_' . bin2hex(random_bytes(8)) . '.' . $fingguVar_ext;
  move_uploaded_file($fingguVar_file['tmp_name'], FINGGU_UPLOAD_DIR . $fingguVar_filename);
  return $fingguVar_filename;
}
```

---

## Pre-Deploy Checklist
- [ ] `display_errors` off in `.htaccess`
- [ ] `.env` file created on server, not committed to git
- [ ] `FINGGU_ENV=production` set
- [ ] DB credentials use production values
- [ ] File permissions: dirs `755`, files `644`, `.env` `600`
- [ ] SSL certificate active on domain
- [ ] `Options -Indexes` in `.htaccess`
- [ ] Remove all `console.log` / `var_dump` debug statements

---

## DO NOT
- Commit `.env` to git — always in `.gitignore`
- Store uploads inside `public_html` — use private directory
- Use `777` permissions on any file or directory
- Hardcode server paths — use `__DIR__` or `dirname()` in PHP
