---
name: payment-and-webhook-safety
description: Financial, wallet, and webhook safety patterns covering idempotency keys, HMAC signature verification, atomic ledger transactions, and double-spend prevention.
when_to_use: "When building payment flows, wallet/balance updates, split-bill ledgers, refunds, or incoming payment/provider webhooks."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Payment & Webhook Safety — Idempotency & Atomic Ledgers

> Money state must never be updated twice, trusted from client amounts, or mutated outside an atomic transaction.

## Core Rules

1. **Idempotency Everywhere**:
   - Require an `Idempotency-Key` on payment, transfer, and payout endpoints; store key + request hash + result atomically.
   - Deduplicate incoming webhooks using the provider's unique `eventId` before processing business logic.
2. **Webhook Verification**:
   - Verify HMAC signatures against the **raw request body** using constant-time comparison (`crypto.timingSafeEqual` / `hmac.Equal`) and check timestamp tolerance to prevent replay attacks.
   - Acknowledge quickly (`2xx`) and enqueue heavy work asynchronously.
3. **Atomic Ledger & Concurrency**:
   - Never compute `balance = read() + delta` in application memory without a DB transaction, row lock (`SELECT ... FOR UPDATE`), or atomic conditional update (`balance >= amount`).
   - Store money as integer minor units (e.g., VND/cents) or fixed-precision `Decimal`, never binary floating-point (`float`/`number`).
   - Record an immutable append-only ledger entry for every credit/debit.
4. **Never Trust Client Totals**:
   - Recompute prices, fees, discounts, and split amounts on the server from authoritative records.

## Verification Checklist

- Concurrent duplicate requests with the same `Idempotency-Key` or webhook `eventId` apply the state transition at most once.
- Invalid or replayed webhook signatures are rejected with `401`/`400`.
- Negative balances or race-condition double-spends are prevented at the database layer.
