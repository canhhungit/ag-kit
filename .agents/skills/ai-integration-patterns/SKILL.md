---
name: ai-integration-patterns
description: Production LLM/AI integration patterns covering structured outputs, prompt-injection defense, token/cost budgets, streaming, timeouts, and model fallbacks.
when_to_use: "When integrating Gemini, OpenAI, or other LLM APIs, building AI review/chat pipelines, enforcing JSON/Zod schemas, or handling streaming responses."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# AI Integration Patterns — Reliable & Safe LLM Pipelines

> Treat LLM outputs as untrusted external input: validate schemas, bound tokens and latency, and isolate untrusted user/repo content.

## Core Rules

1. **Structured Outputs**:
   - Request JSON/structured output and validate every response with `Zod` / `Pydantic` / JSON Schema before using it in business logic or DB writes.
   - Handle malformed or partial outputs gracefully with bounded retries or a safe fallback.
2. **Prompt-Injection Defense**:
   - Clearly delimit untrusted user input, PR diffs, or scraped web content from system instructions.
   - Never grant an LLM direct unvalidated execution of destructive shell/SQL/write actions based solely on untrusted content.
3. **Cost, Latency & Reliability**:
   - Enforce `maxTokens`, request timeouts (`AbortController` / `context.WithTimeout`), and per-user/per-org rate limits.
   - Truncate or chunk large inputs (diffs, logs, docs) before sending to the model.
   - Configure model fallbacks and circuit breakers for upstream `429` / `5xx` responses.
4. **Streaming & Observability**:
   - Stream tokens (SSE / WebSocket) for interactive UX, and log model name, latency, token usage, and failure reason (never log sensitive user secrets).

## Verification Checklist

- Every LLM response passes schema validation before downstream execution.
- Upstream timeouts and `429`/`5xx` errors return clean, user-friendly fallback states.
- Prompt templates separate trusted system instructions from untrusted data.
