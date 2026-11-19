---
name: realtime-fanout
description: Real-time streaming and fan-out architecture patterns covering binary ingestion, Redis Pub/Sub and TimeSeries, sharded WebSocket hubs, batch flushing, and backpressure.
when_to_use: "When designing or debugging high-throughput WebSocket gateways, market/telemetry ingestion pipelines, Redis Pub/Sub fan-out, or DLQ retry workers."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Real-Time Fan-Out — Streaming & WebSocket Gateway Patterns

> Decouple ingestion from fan-out, shard shared state, batch writes, and shed slow consumers before memory grows unbounded.

## Reference Pipeline

```text
Upstream Feed (WS / msgpack / binary)
  -> Ingestion Service (normalize, isolate per-tick engines)
  -> Redis (Pub/Sub + TimeSeries / Streams)
  -> Sharded WS Gateway (N shards + batched Flusher + backpressure)
  -> Downstream Clients / Workers (with DLQ retry)
```

## Core Patterns

1. **Ingestion Isolation**: Keep upstream decoding (`msgpack`, protobuf, JSON) and normalization separate from downstream fan-out. Run per-tick detectors/matchers as isolated consumers so slow analytics never block tick ingestion.
2. **Sharded Hub Locking**: Partition connected WebSocket clients across `N` shards (e.g., 16 or 32 shards hashed by client ID or symbol) instead of a single global `sync.RWMutex` / `Map` lock.
3. **Batched Flusher**: Coalesce high-frequency updates per symbol/channel on a short tick window (e.g., `25ms–50ms`) before serializing and broadcasting to clients, reducing syscall and GC pressure.
4. **Bounded Buffers & Backpressure**: Give every client a bounded outbound channel/queue. If the queue fills (slow consumer), drop stale ticks or close the connection cleanly rather than letting memory grow unbounded.
5. **Reliable Notifications (DLQ)**: For non-ephemeral side effects (push notifications, webhooks, alerts), publish to a queue with exponential backoff and a Dead-Letter Queue (DLQ) for failed deliveries.

## Pitfalls to Avoid

- Broadcasting on a single global lock across thousands of sockets.
- Unbounded per-client send queues causing OOM during traffic spikes.
- Doing synchronous DB writes inside the hot WebSocket/tick-handling loop.
- Reconnecting upstream feeds without jittered exponential backoff.
