---
name: nestjs-fastify
description: Production NestJS backend architecture on Fastify with Mongoose, Redis caching, Pino logging, Terminus health checks, Throttler, and SWC builds.
when_to_use: "When building or reviewing NestJS APIs running on @nestjs/platform-fastify, Mongoose schemas, Pino logging, Swagger docs, or Redis caching."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# NestJS + Fastify — High-Throughput API Patterns

> Fastify performance with strict NestJS module boundaries, schema validation, and production observability.

## Core Stack & Conventions

- **HTTP Adapter**: `@nestjs/platform-fastify` + `@fastify/helmet` (never mix Express-specific middleware/types like `express.Request` or `res.json`).
- **Database**: `@nestjs/mongoose` + `mongoose-paginate-v2` for paginated queries; index query/sort fields explicitly in schemas.
- **Caching & State**: `@nestjs/cache-manager` backed by `@keyv/redis` + `ioredis`.
- **Logging**: `nestjs-pino` + `pino-http` for structured JSON logs with request correlation IDs (`req.id`); avoid `console.log`.
- **Validation & Env**: `class-validator` + `class-transformer` (with global `ValidationPipe({ whitelist: true, forbidNonWhitelisted: true, transform: true })`) and `zod` for typed `@nestjs/config` env validation.
- **Resilience & Docs**: `@nestjs/throttler` for rate limiting, `@nestjs/terminus` for `/health` readiness/liveness probes, `@nestjs/swagger` for OpenAPI specs, and `@swc/core` for fast compilation/testing.

## Architecture Rules

1. **Fastify Compatibility**: Use Fastify hooks/plugins (`@fastify/helmet`, `@fastify/static`) and `FastifyReply` / `FastifyRequest` if touching raw adapter objects.
2. **DTO Strictness**: Every controller body/query/param must use a validated DTO class or Zod schema; never pass raw `@Body()` directly to Mongoose `.create()` or `.findOneAndUpdate()`.
3. **Lean Queries**: Use `.lean()` on read-only Mongoose queries to skip hydration overhead, and always project only required fields.
4. **Pagination Bounds**: Cap `limit` (e.g., max `100`) in pagination DTOs to prevent unbounded collection scans.
5. **Health & Graceful Shutdown**: Enable `app.enableShutdownHooks()` and expose Terminus checks for MongoDB and Redis.

## Verification Checklist

- `pnpm build` (or `npm run build`), `pnpm lint`, and `pnpm test` / `pnpm test:e2e` pass.
- No Express-only types (`Request`/`Response` from `express`) remain in Fastify-backed modules.
- Global `ValidationPipe` strips unknown properties (`whitelist: true`).
