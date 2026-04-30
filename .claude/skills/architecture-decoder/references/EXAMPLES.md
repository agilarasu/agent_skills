# Examples (Architecture Decoder)

## Example prompt: whole app overview

User:
“Explain the architecture of this repo and how requests flow end-to-end.”

Assistant output should:
- Produce the architecture map
- Name the primary flow(s)
- Call out auth boundary, data stores, async systems
- Include 3–5 own-it questions + one concrete test

## Example prompt: trace one endpoint

User:
“Trace the flow for `POST /api/login` end to end.”

Assistant output should:
- Identify handler/controller + middleware
- Explain auth token/session creation
- Mention rate limiting + logging redaction
- Include security posture + failure modes

## Example prompt: webhook ingestion

User:
“How does webhook ingestion work here? Is it secure and scalable?”

Assistant output should:
- Verify signature + replay protection + idempotency
- Explain async offload (queue) and retry/DLQ semantics
- Note scaling levers (consumer concurrency, partitioning) and bottlenecks

## Example prompt: concurrency bug hunt

User:
“We sometimes double-charge customers. Where could concurrency be causing this?”

Assistant output should:
- Identify side-effect boundary (payment API call)
- Require idempotency key + unique constraint/dedupe
- Discuss retries, timeouts, and at-least-once delivery
- Suggest one targeted test (forced retry / duplicate webhook)
