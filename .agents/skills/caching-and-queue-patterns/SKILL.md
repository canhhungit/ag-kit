---
name: caching-and-queue-patterns
description: Caching and background job patterns covering cache-aside, TTL jitter, stampede prevention, invalidation, and retryable queues with Dead-Letter Queues (DLQ).
when_to_use: "When designing Redis caching layers, preventing cache stampedes, or building background job workers (Asynq, BullMQ) with retries and DLQs."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Caching & Queue Patterns — Resilience at Scale

> Cache for speed with bounded staleness; queue for reliability with idempotent consumers and a DLQ.

## Caching Rules

1. **Cache-Aside & TTL Jitter**:
   - Read cache → on miss, fetch from DB → populate cache with a bounded TTL plus random jitter (`ttl + rand(0, 10%)`) to avoid synchronized mass expiration.
2. **Stampede & Penetration Protection**:
   - Coalesce concurrent misses for the same hot key (`singleflight` / distributed lock) so only one request hits the DB.
   - Cache negative lookups briefly (`30s–60s`) when guarding against repeated misses on non-existent IDs.
3. **Explicit Invalidation**:
   - Invalidate or update cache keys on write/mutation paths; keep key naming deterministic (`entity:v1:{id}`).
   - Never store unbounded lists or sensitive unencrypted secrets in shared caches without strict key scoping.

## Background Queue Rules

1. **Idempotent Job Handlers**: Every job must be safe to execute more than once (at-least-once delivery).
2. **Bounded Retries & Backoff**: Configure max retry attempts with exponential backoff + jitter; classify transient errors (network/timeout) vs permanent errors (bad payload).
3. **Dead-Letter Queue (DLQ)**: Route exhausted jobs to a DLQ with error context and correlation ID so operators can inspect and replay safely.
4. **Job Timeouts**: Set explicit execution timeouts per job type so stuck workers do not starve the queue.
