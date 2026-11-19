---
name: security-rules
version: 1.0.0
priority: P0
trigger: model_decision
description: Apply whenever writing, reviewing, or modifying API endpoints, auth, database queries, webhooks, payments, file uploads, or secret/config handling.
---

# Security Rules (P0) - AG Kit

> Security is non-negotiable. Validate at every trust boundary and fail closed.

## Mandatory Guardrails

1. **Secrets & Config**:
   - Never hardcode API keys, JWT secrets, passwords, or private keys in source code or tests.
   - Never commit `.env` files or log `authorization`, `cookie`, `token`, or `password` headers/fields.
2. **Input & Injection Defense**:
   - Validate and sanitize all external inputs (body, query, params, headers, webhooks) at the boundary with a strict schema (`Zod`, `class-validator`, `Pydantic`, Go validator).
   - Always use parameterized queries / ORM bindings; never concatenate user input into SQL, NoSQL, shell commands, or `eval`.
3. **Authentication & Authorization**:
   - Enforce authentication and resource ownership / RBAC checks on the server for every protected route — never rely solely on UI hiding.
   - Store passwords with `argon2id` or `bcrypt`; use `HttpOnly`, `Secure`, `SameSite` cookies for browser sessions.
4. **Webhooks, Payments & Files**:
   - Verify webhook signatures using constant-time HMAC comparison against the raw body.
   - Use atomic DB transactions + idempotency keys for balance/ledger updates; never trust client-submitted prices or totals.
   - Restrict file uploads by MIME/magic bytes, size limits, and safe storage paths (prevent path traversal).
