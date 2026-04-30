# Security Deep Dive (Architecture Decoder)

Use this file when the user mentions security, auth, permissions, OWASP, secrets, threat modeling, compliance, PII/PHI, webhooks, uploads, or “is this safe?”.

## Fast threat model (practical)

1. **Assets**
   - Credentials (passwords, sessions, tokens, API keys)
   - PII/PHI and regulated data
   - Money movement (payments, refunds), admin actions
   - Infrastructure access (cloud roles, CI secrets)

2. **Entry points**
   - Public HTTP endpoints, auth endpoints
   - Webhooks (untrusted caller)
   - File uploads, CSV imports
   - Internal admin tools, cron/worker consumers

3. **Trust boundaries**
   - Browser/mobile ↔ API edge
   - Public internet ↔ webhook handler
   - Service ↔ database/cache/queue
   - Service ↔ third-party API

4. **Top risks to check first**
   - **AuthN**: token/session verification location, expiration, revocation
   - **AuthZ**: object-level permissions (IDOR), role checks, multi-tenant isolation
   - **Input validation**: schema validation, allowlists, server-side enforcement
   - **Secrets**: stored in env/secret manager; never in repo; rotated
   - **Injection**: SQL/NoSQL injection, command injection, SSRF
   - **XSS/CSRF** (web): output encoding, CSRF tokens, same-site cookies
   - **File uploads**: content-type spoofing, AV scanning, path traversal, public ACLs
   - **Webhooks**: signature validation + replay protection + idempotency
   - **Rate limiting**: brute force login, password reset, expensive endpoints
   - **Logging**: no secrets/PII in logs; structured audit logs for sensitive actions

## AuthN/AuthZ walkthrough prompts

When explaining auth, answer explicitly:
- Where does identity come from (cookie, bearer token, mTLS, API key)?
- Where is it verified (middleware, gateway, per-handler)?
- What does “principal” contain (user id, tenant id, roles, scopes)?
- Where is authorization enforced (central policy vs scattered checks)?
- Is authorization **object-level** (e.g., `record.tenantId == principal.tenantId`)?

## Webhook handler checklist

- [ ] Verify signature (HMAC/public key) and reject missing/invalid signatures
- [ ] Validate timestamp + replay window
- [ ] Use idempotency key (event id) and dedupe
- [ ] Enforce allowlisted event types and schema validation
- [ ] Store raw payload safely (optional) for forensics without leaking secrets
- [ ] Return fast ack; do heavy work asynchronously when possible

## Data protection checklist (PII/PHI)

- [ ] Data classification: what’s PII/PHI and where is it stored?
- [ ] Encryption in transit: TLS everywhere (internal too, if feasible)
- [ ] Encryption at rest: DB/storage encryption + key management
- [ ] Least privilege: DB users/roles, cloud IAM, scoped tokens
- [ ] Retention & deletion: TTLs, DSAR/delete flows, backups handling

## “Red flags” to call out (high signal)

- Secrets committed to repo or logged
- AuthZ only in UI (“frontend checks”) but not server-side
- Tenant id taken from request body instead of principal
- Webhooks without signature validation
- Unbounded retries without backoff/jitter
- Missing timeouts on external calls
