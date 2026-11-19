---
name: dependency-manager
description: Safe dependency and lockfile management covering vulnerability auditing, overrides/resolutions, version pinning, and supply-chain hygiene.
when_to_use: "When adding, upgrading, or auditing packages in package.json, go.mod, requirements.txt, or resolving security advisories."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Dependency Manager — Supply-Chain & Upgrade Hygiene

> Every dependency is code you own in production — keep the tree minimal, locked, and audited.

## Core Rules

1. **Lockfile Integrity**:
   - Always commit lockfiles (`pnpm-lock.yaml`, `package-lock.json`, `go.sum`) and use deterministic installs in CI (`pnpm install --frozen-lockfile`, `npm ci`).
   - Never manually edit lockfiles; use the package manager CLI.
2. **Security & Overrides**:
   - Run audits (`pnpm audit --prod`, `npm audit`, `govulncheck ./...`) before releases.
   - Patch vulnerable transitive dependencies using `pnpm.overrides` / `overrides` when a direct upgrade is not yet available, and verify tests still pass.
3. **Minimal Footprint**:
   - Prefer standard library or existing project dependencies over adding new packages for trivial helpers (e.g., `node:crypto`, `node:test`, `clsx`).
   - Check package maintenance, license, and bundle impact before installing.
4. **Controlled Upgrades**:
   - Separate major dependency upgrades from feature commits so regressions are easy to bisect.
   - Pin GitHub Actions to immutable commit SHAs rather than mutable tags.

## Verification Checklist

- Lockfile is synchronized with manifest (`package.json` / `go.mod`) and installs cleanly.
- Zero high/critical production vulnerabilities remain unaddressed.
- Full test suite and typecheck/build pass after dependency changes.
