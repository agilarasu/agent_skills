# Concurrency & Async Deep Dive (Architecture Decoder)

Use this file when the user mentions concurrency, parallelism, race conditions, idempotency, locking, retries, exactly-once, consumers, or “double processing”.

## First: name the concurrency model

Identify where concurrency happens:
- Multi-threaded / async runtime
- Multiple process replicas behind a load balancer
- Multiple worker instances consuming from a queue/stream
- DB-level concurrency (transactions/locks)

Then name the shared resources: DB rows, cache keys, files, external side effects.

## The big three: idempotency, ordering, retries

### Idempotency
Explain how duplicates are handled:
- **Key**: request id, message id, webhook event id, user+action+timestamp bucket
- **Storage**: idempotency table, unique constraint, dedupe cache with TTL
- **Scope**: per endpoint, per tenant, per side effect

### Ordering
Determine if ordering is required:
- If required, where is it enforced? (partition key, single-threaded consumer per key)
- If not required, document that explicitly to avoid hidden coupling.

### Retries
Specify:
- Which failures retry (transient) vs not (validation/auth)
- Backoff + jitter
- Max attempts + dead letter queue
- Whether retries can re-trigger side effects

## Transactions and consistency (DB)

Ask/answer:
- What must be atomic?
- Where is the transaction boundary?
- Is the system okay with eventual consistency?

Call out classic hazards:
- Lost updates (read-modify-write without proper isolation)
- Double insert without unique constraints
- Long transactions causing lock contention

## The Outbox pattern (when needed)

If you see “write to DB then publish event”, recommend the robust shape:
- Write domain change + outbox record in one transaction
- A separate dispatcher publishes outbox events
- Consumer side is idempotent

## “Exactly once” clarification

Unless the system has a provable transactional boundary across publish + state, treat it as:
- **At-least-once delivery**, requiring idempotent handlers

## Concurrency red flags (call out explicitly)

- Side effects executed before dedupe check
- Retry loops without idempotency keys
- Non-unique natural keys (no DB constraint) in concurrent writes
- Cache used as source of truth without consistency plan
- Multiple consumers updating the same row without locking/versioning
