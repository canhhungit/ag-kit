---
name: go-gin-pro
description: Production Go backend architecture using Gin, Google Wire dependency injection, typed env config, MongoDB, Redis, and Asynq background queues.
when_to_use: "When building or refactoring Go HTTP services, Gin handlers/middlewares, Wire DI graphs, Asynq workers, or MongoDB/Redis repositories in Go."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Go Gin Pro — Production Service Patterns

> Idiomatic Go services with explicit dependency wiring, bounded concurrency, and fail-fast configuration.

## Core Stack & Conventions

- **Web Framework**: `github.com/gin-gonic/gin` with thin handlers (bind/validate → call service → map error to HTTP status).
- **Dependency Injection**: `github.com/google/wire` (`wire.go` + generated `wire_gen.go`) to keep constructors explicit and testable without reflection magic.
- **Typed Config**: `github.com/caarlos0/env/v11` + `godotenv` — parse and validate all env vars at startup; fail immediately on missing required secrets.
- **Data & Cache**: `go.mongodb.org/mongo-driver` (pass `context.Context` with timeouts to every query) and `github.com/redis/go-redis/v9`.
- **Async Jobs**: `github.com/hibiken/asynq` for retryable background tasks, scheduled jobs, and dead-letter handling.
- **Validation**: `go-playground/validator/v10` via Gin binding tags (`binding:"required,..."`).

## Architecture Rules

1. **Layer Separation**: `handler` (HTTP/DTO) → `service` (business logic, interfaces) → `repository` (MongoDB/Redis). Never leak `*gin.Context` into `service` or `repository`.
2. **Context Propagation**: Always pass `ctx context.Context` from `c.Request.Context()` down to DB/Redis/HTTP calls; enforce per-operation timeouts (`context.WithTimeout`).
3. **Graceful Shutdown**: Listen for `SIGINT`/`SIGTERM`, call `srv.Shutdown(ctx)` with a bounded deadline, and drain Asynq workers + close MongoDB/Redis clients before exiting.
4. **Security**: Use `crypto/hmac` (`hmac.Equal` for constant-time comparison) for webhook/API signatures and `crypto/aes` + `cipher.NewGCM` for payload encryption. Never log raw tokens or secrets.
5. **Error Handling**: Wrap errors with `fmt.Errorf("...: %w", err)` and map domain sentinel errors (`ErrNotFound`, `ErrUnauthorized`, `ErrConflict`) cleanly in one middleware or response helper.

## Verification Checklist

- `go vet ./...` and `go test -race ./...` pass with zero race conditions.
- If using Wire, regenerate with `wire ./...` and verify `wire_gen.go` compiles.
- Confirm all DB/Redis/HTTP calls use bounded `context.Context` timeouts.
