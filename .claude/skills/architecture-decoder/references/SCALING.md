# Scaling & Performance Deep Dive (Architecture Decoder)

Use this file when the user mentions scaling, performance, latency, throughput, load tests, bottlenecks, caching, queues/lag, or “will this handle N users?”.

## Start with the constraint (pick likely limiter)

Name the likely limiting resource:
- **CPU** (serialization, crypto, heavy compute)
- **IO** (DB, network calls, disk)
- **DB connections** (pool exhaustion, slow queries)
- **Queue lag** (consumers can’t keep up)
- **Third-party rate limits** (payments, SMS, email)

Then explain the scaling lever: vertical scale, horizontal scale, caching, batching, async offload.

## Read the hot path (one flow)

For the chosen end-to-end flow, quantify the “shape”:
- Number of DB roundtrips and whether any are N+1
- Fan-out to internal services and third parties
- Payload sizes (uploads/downloads), serialization overhead
- Any synchronous work that could be async

## Common bottlenecks & fixes (high-signal)

- **Database**
  - Symptoms: slow queries, high p95 latency, lock waits, pool saturation
  - Fixes: indexes, query shape, pagination, reduce N+1, read replicas, caching

- **Caching**
  - What: query results, computed views, sessions, feature flags
  - Risks: stale data, invalidation complexity, cache stampede
  - Fixes: TTL + jitter, request coalescing, negative caching, write-through vs read-through

- **Async workloads**
  - Symptoms: queue lag, reprocessing, long jobs blocking
  - Fixes: increase consumer concurrency (safely), partitioning/sharding, batch consumption, split heavy jobs

- **External APIs**
  - Symptoms: timeouts, retries, throttling
  - Fixes: strict timeouts, circuit breakers, bulkheads, caching, async retries with backoff

## Backpressure & overload behavior

Always answer:
- What happens when requests exceed capacity?
- Where do you shed load (rate limit, queue, 503, degraded mode)?
- What protects the DB from thundering herds?

## Metrics to anchor the explanation

Use these as a minimal set:
- p50/p95/p99 latency per endpoint/consumer
- Throughput (RPS / messages/sec)
- Error rate
- DB: query time, lock time, connection pool usage
- Queue: lag, age of oldest message, retries/DLQ rate

## Quick SLO framing

If the user hasn’t provided targets, assume:
- API: p95 < 300–800ms for common endpoints
- Workers: bounded lag (e.g., p95 event processing < a few minutes)

Call out what would break these targets first.
