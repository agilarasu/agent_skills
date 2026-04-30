---
name: architecture-decoder
description: >
  Explain an application's architecture and end-to-end flow (request lifecycle, data flow, async boundaries) from code and configs. Emphasize security, scaling/performance, reliability, and concurrency risks. Use when the user asks “explain the architecture”, “system design”, “high level design”, “how does this app work”, “trace the flow”, “request lifecycle”, “end to end”, “from UI to DB”, “threat model/auth/permissions/OWASP”, “scale/load/latency/throughput”, or “race condition/parallel/idempotency/locking”.
---

# Architecture Decoder Skill

This skill helps developers build a mental model of an application: **components, boundaries, and the end-to-end flow** (sync + async), with explicit attention to **security**, **scaling/performance**, **concurrency**, and **operational risks**.

## Output Modes

### Default Mode
Reply in chat as normal.

### Markdown Mode
Triggered when the user includes `--md`, `--markdown`, `md`, or `markdown` anywhere in their message.

When markdown mode is triggered:
1. Write the full explanation as a `.md` file instead of replying in chat
2. Save it to `~/architecture-explanations/` (create if missing)
3. Name the file based on context: `[system-or-feature]-architecture.md` or `architecture-[timestamp].md`
4. Use the same structure as the chat output (headings + checkboxes)
5. After saving, reply in chat with only: the filename, the full path, and one line confirming what was saved — nothing else

## Decoding Process (Architecture + Flow)

### Step 0: Establish the “unit of explanation”
Decide what you’re explaining based on user input:
- **Whole app**: one-page architecture + key flows
- **One flow**: e.g. “Login”, “Checkout”, “Webhook ingestion”, “Kafka worker”
- **One subsystem**: e.g. “API”, “DB layer”, “Background jobs”, “Auth”

If scope is unclear, ask exactly one question: “Which user journey or endpoint should we trace end-to-end?”

### Step 1: Identify the stack and runtime shape
From repo signals (package files, Docker/K8s, CI, env vars), infer:
- **Runtime units**: browser/mobile, API, workers, cron, queues/streams, DB, cache, object storage, third-party APIs
- **Communication**: HTTP, gRPC, websockets, pub/sub, Kafka, SQS, cron
- **State**: where the source of truth lives (DB), caches, client state, derived state

### Step 2: The architecture anchor (mental map)
Start with a compact map before details:

```
[Client/UI] → [Edge/API] → [Services] → [Data stores]
                 ↓
             [Async: queue/stream/cron] → [Workers] → [Data stores]
```

Then list components as bullets with **responsibility + boundary** (what it owns / what it must not do).

### Step 3: Trace one flow end-to-end (happy path first)
Pick the most representative flow (or the user’s requested one) and narrate:
- **Entry point**: route/controller/handler, message consumer, scheduled job
- **AuthN/AuthZ**: where identity is established, permissions checked
- **Validation**: schema/DTO, constraints, error semantics
- **Business logic**: services/use-cases/domain layer
- **Data access**: repository/ORM calls, transactions, consistency expectations
- **Side effects**: events emitted, emails, webhooks, file writes
- **Response/ack**: HTTP response, consumer ack/commit, retry policy

Use a numbered “timeline” so a teammate can follow it.

### Step 4: Call out async + concurrency boundaries explicitly
Whenever the flow crosses an async boundary, explicitly note:
- **Delivery semantics**: at-most-once / at-least-once / exactly-once (effective)
- **Idempotency key**: what makes repeats safe
- **Ordering**: required? guaranteed?
- **Backpressure**: what happens under load

If the user’s question mentions concurrency, also load and follow `CONCURRENCY.md`.

### Step 5: Security & trust boundaries (threat-aware)
Name the trust boundaries and primary assets:
- **Assets**: credentials, PII/PHI, payment tokens, admin actions, API keys
- **Attack surface**: endpoints, webhooks, uploads, auth flows, internal admin panels
- **Controls**: authentication, authorization, input validation, rate limits, secrets, auditing

If the user’s question mentions security, auth, OWASP, secrets, permissions, or threat modeling, load and follow `SECURITY.md`.

### Step 6: Scaling/performance (how it behaves under load)
Give a pragmatic scalability read:
- **Bottlenecks**: DB hotspots, N+1, chatty service calls, synchronous fan-out
- **Scaling axis**: CPU, IO, DB connections, queue lag, external API limits
- **Caching strategy**: what is cached, TTL/invalidations, stampede protection
- **SLOs**: latency/throughput targets and where they’re likely violated

If the user mentions scale, performance, latency, throughput, load testing, or bottlenecks, load and follow `SCALING.md`.

### Step 7: Reliability and ops “own it” checks
Always include a short checklist:
- [ ] Failure modes: which dependencies fail and what degrades vs breaks?
- [ ] Retries/timeouts: where are they configured and are they safe (no retry storms)?
- [ ] Observability: logs/metrics/traces — what would you look at first?
- [ ] Data safety: migrations, backups, destructive ops guarded?

## Default Output Template (Chat or Markdown)

Use this structure:

```
**What this system does (plain English):**
[1 sentence]

**Architecture map (components & boundaries):**
- Client/UI: ...
- API/Gateway: ...
- Service(s): ...
- Async: queue/stream/cron + workers: ...
- Data stores: ...
- Third-party: ...

**One end-to-end flow (happy path):**
1) ...
2) ...
3) ...

**Security posture (high-signal):**
- Trust boundaries: ...
- Primary risks: ...
- Key controls: ...

**Scaling & performance notes:**
- Bottlenecks: ...
- Likely limits: ...
- Quick wins: ...

**Concurrency/async risks:**
- Semantics: ...
- Idempotency: ...
- Ordering: ...

**Before you ship — can you answer these?**
- [ ] ...
- [ ] ...
- [ ] ...

**One thing to test:**
[concrete test scenario]
```

## Deep Dives (read only when relevant)

- Security/threat modeling: `references/SECURITY.md`
- Scaling/performance: `references/SCALING.md`
- Concurrency/async/idempotency: `references/CONCURRENCY.md`
- Ready-to-use checklists + prompts: `references/CHECKLISTS.md`
- Example writeups: `references/EXAMPLES.md`
