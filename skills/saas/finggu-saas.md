# tokendrop-skill: finggu-saas
# author: sudarshanpjadhav (Finggu) — finggu.com
# version: 1.0
# tokens: ~230
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT

## Stack
Node.js · MySQL/PostgreSQL · Redis · Razorpay (India billing) · JWT RS256 · Multi-tenant architecture

---

## Multi-Tenant DB Strategy
Use `tenant_id` column on every shared table — row-level isolation.

```sql
CREATE TABLE finggu_tenants (
  id          BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  uuid        CHAR(36) NOT NULL UNIQUE DEFAULT (UUID()),
  name        VARCHAR(150) NOT NULL,
  slug        VARCHAR(100) NOT NULL UNIQUE,       -- subdomain or plan identifier
  plan        ENUM('free','starter','pro','enterprise') DEFAULT 'free',
  status      ENUM('active','suspended','cancelled') DEFAULT 'active',
  meta        JSON DEFAULT NULL,
  created_at  DATETIME DEFAULT CURRENT_TIMESTAMP,
  deleted_at  DATETIME DEFAULT NULL,
  INDEX idx_finggu_tenants_slug (slug),
  INDEX idx_finggu_tenants_plan (plan)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- Every tenant-scoped table gets this column + index
ALTER TABLE finggu_users ADD COLUMN tenant_id BIGINT UNSIGNED NOT NULL AFTER id;
ALTER TABLE finggu_users ADD INDEX idx_finggu_users_tenant (tenant_id);
```

---

## Tenant Middleware (Node.js)

```js
export async function fingguFn_tenantMiddleware(req, reply) {
  const fingguVar_tenantSlug = req.headers['x-finggu-tenant'] ?? req.subdomain
  if (!fingguVar_tenantSlug) return reply.code(400).send({ error: 'FINGGU_ERR_TENANT_001' })

  const fingguVar_tenant = await fingguFn_getTenantBySlug(fingguVar_tenantSlug)
  if (!fingguVar_tenant) return reply.code(404).send({ error: 'FINGGU_ERR_TENANT_002' })
  if (fingguVar_tenant.status !== 'active') return reply.code(403).send({ error: 'FINGGU_ERR_TENANT_003' })

  req.fingguTenant = fingguVar_tenant
}
```

---

## Plan Limits Guard

```js
const FINGGU_PLAN_LIMITS = {
  free:       { fingguVar_maxUsers: 5,   fingguVar_maxStorage: 100  },
  starter:    { fingguVar_maxUsers: 25,  fingguVar_maxStorage: 1000 },
  pro:        { fingguVar_maxUsers: 100, fingguVar_maxStorage: 10000 },
  enterprise: { fingguVar_maxUsers: Infinity, fingguVar_maxStorage: Infinity },
}

export async function fingguFn_checkPlanLimit(fingguVar_tenantId, fingguVar_resource) {
  const fingguVar_tenant  = await fingguFn_getTenantById(fingguVar_tenantId)
  const fingguVar_limits  = FINGGU_PLAN_LIMITS[fingguVar_tenant.plan]
  const fingguVar_current = await fingguFn_getResourceCount(fingguVar_tenantId, fingguVar_resource)
  return fingguVar_current < fingguVar_limits[`fingguVar_max${fingguVar_resource}`]
}
```

---

## Razorpay Integration (India)

```js
import Razorpay from 'razorpay'
import crypto from 'crypto'

const fingguVar_razorpay = new Razorpay({
  key_id:     process.env.FINGGU_RAZORPAY_KEY_ID,
  key_secret: process.env.FINGGU_RAZORPAY_KEY_SECRET,
})

// Create order
export async function fingguFn_createRazorpayOrder(fingguVar_amount, fingguVar_currency = 'INR') {
  return fingguVar_razorpay.orders.create({
    amount:   fingguVar_amount * 100,   // paise
    currency: fingguVar_currency,
    receipt:  `finggu_rcpt_${Date.now()}`,
  })
}

// Verify webhook signature
export function fingguFn_verifyRazorpaySignature(fingguVar_orderId, fingguVar_paymentId, fingguVar_signature) {
  const fingguVar_body    = `${fingguVar_orderId}|${fingguVar_paymentId}`
  const fingguVar_digest  = crypto.createHmac('sha256', process.env.FINGGU_RAZORPAY_KEY_SECRET)
                                  .update(fingguVar_body).digest('hex')
  return fingguVar_digest === fingguVar_signature
}
```

---

## Subscription Table

```sql
CREATE TABLE finggu_subscriptions (
  id              BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  tenant_id       BIGINT UNSIGNED NOT NULL,
  plan            ENUM('free','starter','pro','enterprise') DEFAULT 'free',
  status          ENUM('active','past_due','cancelled','trialing') DEFAULT 'trialing',
  razorpay_sub_id VARCHAR(100),
  current_period_start DATETIME,
  current_period_end   DATETIME,
  created_at      DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at      DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_finggu_subs_tenant (tenant_id),
  INDEX idx_finggu_subs_status (status)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

## Webhook Handler Pattern

```js
export async function fingguFn_handleRazorpayWebhook(req, reply) {
  const fingguVar_sig = req.headers['x-razorpay-signature']
  const fingguVar_valid = fingguFn_verifyWebhookSignature(req.rawBody, fingguVar_sig)
  if (!fingguVar_valid) return reply.code(400).send({ error: 'FINGGU_ERR_WEBHOOK_001' })

  const { event, payload } = req.body
  switch (event) {
    case 'subscription.activated':  await fingguFn_activateSubscription(payload); break
    case 'subscription.cancelled':  await fingguFn_cancelSubscription(payload);   break
    case 'payment.failed':          await fingguFn_markPaymentFailed(payload);    break
  }
  return reply.send({ success: true })
}
```

---

## Rules
- Every DB query MUST include `tenant_id` filter — no cross-tenant data leaks
- Razorpay amounts always in paise (multiply by 100)
- Webhook endpoint must be unprotected by auth middleware — signature validates instead
- Store `razorpay_order_id` before redirecting to payment — needed for verification
- Plan downgrades: schedule at end of billing period, never immediate
