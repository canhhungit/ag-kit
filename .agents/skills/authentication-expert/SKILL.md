---
name: authentication-expert
description: Authentication and authorization patterns covering JWT/session management, OAuth2/OIDC, RBAC/ABAC, token rotation, secure cookies, and CSRF/XSS protection.
when_to_use: "When implementing login, signup, OAuth2/OIDC, JWT refresh rotation, RBAC permissions, password hashing, or session security."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Authentication Expert — Identity, Sessions & Access Control

> Authenticate once, authorize on every request, and never trust client-supplied identity claims.

## Core Rules

1. **Password Storage**: Use `argon2id` or `bcrypt` (cost `>= 12`) with per-user salts. Never store plaintext or fast-hash (`SHA-256`/`MD5`) passwords.
2. **Sessions & Tokens**:
   - Prefer `HttpOnly`, `Secure`, `SameSite=Lax` (or `Strict`) cookies over `localStorage` for browser sessions.
   - Keep access tokens short-lived (`5–15 min`); rotate refresh tokens on every use and revoke the token family on reuse detection.
   - Always verify `alg`, `iss`, `aud`, and `exp` on JWT verification; reject `"none"` algorithm.
3. **Authorization (AuthZ)**:
   - Enforce RBAC/ABAC and resource ownership checks (`resource.ownerId === user.id`) on the server for every endpoint, never only in UI routes.
   - Default-deny on missing roles or permissions.
4. **Abuse & CSRF Protection**:
   - Rate-limit login, OTP, password-reset, and token endpoints.
   - Use CSRF tokens or `SameSite` + `Origin`/`Referer` validation for cookie-authenticated state-changing requests.
   - Return generic auth errors (`"Invalid credentials"`) to prevent user enumeration.

## Verification Checklist

- Protected routes fail closed (`401`/`403`) when token/session is missing, expired, or lacks ownership.
- Refresh token reuse invalidates the session chain.
- Secrets (`JWT_SECRET`, OAuth client secrets) are loaded from env and never hardcoded or logged.
