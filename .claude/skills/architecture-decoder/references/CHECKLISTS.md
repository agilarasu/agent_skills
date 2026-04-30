# Checklists & Prompts (Architecture Decoder)

Use these to keep architecture explanations consistent and “ownable”.

## Architecture inventory (components)

- [ ] Client/UI apps (web/mobile) and their responsibilities
- [ ] API edge (gateway/BFF) and auth enforcement point
- [ ] Core services/modules (bounded contexts)
- [ ] Async systems (queue/stream/cron) and workers
- [ ] Data stores (DBs, caches, search, object storage)
- [ ] Third-party dependencies (payments, email, analytics)

## Flow trace prompts (pick one)

- “Trace request lifecycle for endpoint X from the moment it hits the server until the response is returned.”
- “Trace event lifecycle for message type Y from publish → consume → side effects.”
- “Trace data lifecycle for entity Z: create → update → read model → delete/retention.”

## Reliability checklist (minimum)

- [ ] Timeouts exist on all network calls
- [ ] Retries have backoff + jitter and are bounded
- [ ] Circuit breaker / bulkhead for flaky dependencies (if needed)
- [ ] DLQ or poison-message handling for async
- [ ] Graceful degradation for non-critical dependencies

## Security checklist (minimum)

- [ ] AuthN location is clear and centralized
- [ ] AuthZ is object-level (IDOR-safe) and tenant-safe
- [ ] Input validation is server-side and schema-based
- [ ] Secrets are not in repo/logs; rotated and scoped
- [ ] Webhooks are signed + replay-protected + idempotent

## Scaling checklist (minimum)

- [ ] DB access is bounded (pagination, indexes, no N+1)
- [ ] Caching plan exists (what, TTL, invalidation)
- [ ] Async offload for slow side effects (email/SMS/fan-out)
- [ ] Backpressure strategy is explicit (rate limiting / queueing / 503)
- [ ] Observability covers p95 latency, error rate, queue lag, DB pool

## “Own it” questions (always include 3–5)

- [ ] What are the top 2 dependencies that can take this flow down?
- [ ] Where are the trust boundaries and how are they enforced?
- [ ] What happens on duplicate messages/requests?
- [ ] What’s the first dashboard/log you’d check for a p95 spike?
- [ ] What’s the rollback plan for a bad deploy/migration?
