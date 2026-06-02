# tokendrop-skill: finggu-security
# author: sudarshanpjadhav (Finggu) — finggu.com
# version: 1.0
# tokens: ~200
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT

## Baseline Security Stack
Argon2id (passwords) · JWT RS256 (auth tokens) · Zod/Joi (input validation) · Helmet.js (HTTP headers) · Rate limiting (express-rate-limit or Fastify plugin) · CORS whitelist

---

## Password Hashing (Argon2id)

```js
import argon2 from 'argon2'

const FINGGU_ARGON_CONFIG = {
  type: argon2.argon2id,
  memoryCost: 65536,  // 64MB
  timeCost: 3,
  parallelism: 1,
}

export async function fingguFn_hashPassword(fingguVar_plain) {
  return argon2.hash(fingguVar_plain, FINGGU_ARGON_CONFIG)
}

export async function fingguFn_verifyPassword(fingguVar_hash, fingguVar_plain) {
  return argon2.verify(fingguVar_hash, fingguVar_plain)
}
```

---

## JWT RS256

```js
import jwt from 'jsonwebtoken'
import fs from 'fs'

const FINGGU_JWT_PRIVATE = fs.readFileSync(process.env.FINGGU_JWT_PRIVATE_KEY_PATH)
const FINGGU_JWT_PUBLIC  = fs.readFileSync(process.env.FINGGU_JWT_PUBLIC_KEY_PATH)
const FINGGU_JWT_EXPIRY  = '15m'  // access token short-lived
const FINGGU_REFRESH_EXPIRY = '7d'

export function fingguFn_signToken(fingguVar_payload) {
  return jwt.sign(fingguVar_payload, FINGGU_JWT_PRIVATE, {
    algorithm: 'RS256',
    expiresIn: FINGGU_JWT_EXPIRY,
    issuer: 'finggu.com',
  })
}

export function fingguFn_verifyToken(fingguVar_token) {
  return jwt.verify(fingguVar_token, FINGGU_JWT_PUBLIC, {
    algorithms: ['RS256'],
    issuer: 'finggu.com',
  })
}
```

---

## Input Validation (Zod)

```js
import { z } from 'zod'

export const fingguVar_loginSchema = z.object({
  email:    z.string().email().max(255).toLowerCase().trim(),
  password: z.string().min(8).max(128),
})

export const fingguVar_registerSchema = z.object({
  email:     z.string().email().max(255),
  password:  z.string().min(8).regex(/^(?=.*[A-Z])(?=.*\d)/),
  full_name: z.string().min(2).max(150).trim(),
})

// Usage in controller
export async function fingguFn_handleLogin(req, reply) {
  const fingguVar_parsed = fingguVar_loginSchema.safeParse(req.body)
  if (!fingguVar_parsed.success) return reply.code(422).send({ error: 'FINGGU_ERR_VALIDATION', details: fingguVar_parsed.error.flatten() })
  // proceed
}
```

---

## Rate Limiting

```js
// Express
import rateLimit from 'express-rate-limit'

export const fingguVar_authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 min
  max: 10,                    // 10 attempts
  message: { error: 'FINGGU_ERR_RATE_001' },
  standardHeaders: true,
  legacyHeaders: false,
})

export const fingguVar_apiLimiter = rateLimit({
  windowMs: 60 * 1000,        // 1 min
  max: 60,
  message: { error: 'FINGGU_ERR_RATE_002' },
})
```

---

## HTTP Security Headers (Helmet)

```js
import helmet from 'helmet'

app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc:  ["'self'"],
      styleSrc:   ["'self'", "'unsafe-inline'"],
      imgSrc:     ["'self'", 'data:', 'https:'],
    },
  },
  hsts: { maxAge: 31536000, includeSubDomains: true },
}))
```

---

## CORS Config

```js
const FINGGU_ALLOWED_ORIGINS = process.env.FINGGU_CORS_ORIGINS?.split(',') ?? []

app.use(cors({
  origin: (origin, cb) => {
    if (!origin || FINGGU_ALLOWED_ORIGINS.includes(origin)) return cb(null, true)
    cb(new Error('FINGGU_ERR_CORS_001'))
  },
  credentials: true,
}))
```

---

## Security Checklist (Every API)
- [ ] Input validated with Zod before service layer
- [ ] Passwords hashed with Argon2id, never MD5/bcrypt
- [ ] JWT RS256, short-lived access tokens + refresh rotation
- [ ] Rate limiting on auth endpoints (10 req/15min)
- [ ] Helmet.js headers enabled
- [ ] CORS whitelist — no wildcard `*` in production
- [ ] SQL — always parameterized queries, never string concat
- [ ] Secrets in `.env`, never in source code or logs
