---
name: observability-engineer
description: Production observability covering structured logging, correlation IDs, OpenTelemetry tracing, Prometheus metrics, Sentry error tracking, and health probes.
when_to_use: "When adding logging, metrics, distributed tracing, Sentry error reporting, or /health readiness/liveness endpoints."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Observability Engineer — Logs, Metrics & Traces

> If you cannot measure latency, error rate, and request context in production, you are debugging blind.

## Core Pillars

1. **Structured Logging**:
   - Use structured JSON loggers (`pino`, `logrus`/`slog`, `structlog`) instead of `console.log` or `fmt.Println`.
   - Attach `requestId` / `traceId`, `userId` (non-PII ID), `method`, `path`, `statusCode`, and `durationMs` to every request log.
   - Redact `authorization`, `cookie`, `password`, `token`, and API keys automatically.
2. **Distributed Tracing**:
   - Propagate W3C `traceparent` headers across HTTP, gRPC, and queue workers (OpenTelemetry).
   - Add spans around external API calls, DB queries, and cache operations.
3. **Metrics (RED / USE)**:
   - Track **Rate**, **Errors**, and **Duration** (p50/p95/p99 histograms) per route and background job.
   - Monitor queue depth, DLQ size, DB pool saturation, and WebSocket active connections.
4. **Health & Readiness Probes**:
   - Separate `/health/live` (process alive, fast, no external deps) from `/health/ready` (checks DB, Redis, and critical dependencies with short timeouts).

## Verification Checklist

- Zero sensitive tokens or PII appear in log output.
- Every async worker/webhook preserves correlation IDs from the originating request.
- `/health/ready` returns non-200 when critical DB/Redis dependencies are unreachable.
