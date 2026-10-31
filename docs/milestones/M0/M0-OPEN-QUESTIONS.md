# M0 — open questions for owner review

These are the remaining choices worth discussing before M1/M2. The agent must not silently decide them.

| ID | Question | Current recommendation / options | Needed by |
|---|---|---|---|
| OQ-01 | RabbitMQ vs BullMQ + Redis? | Keep RabbitMQ baseline because cache and message broker remain independent, and it teaches broker concepts. Reconsider if operational complexity slows delivery disproportionately. | M0 sign-off |
| OQ-02 | What authentication model? | Compare secure cookie-backed server sessions vs signed-token approach. Prefer straightforward revocable sessions for a browser-first dashboard; choose password hash implementation only after reviewing current maintained options. | M2 planning |
| OQ-03 | How will local API and web origins work? | Prefer a simple same-site development arrangement or explicit narrow CORS/CSRF design. Do not assume cross-origin cookie behaviour. | M1/M2 |
| OQ-04 | Which redirect status? | Temporary redirect while destinations are editable; assess 302 vs 307 semantics and browser/proxy caching. Avoid permanent caching until behaviour is clear. | M3 |
| OQ-05 | What short-code alphabet and length? | CSPRNG-generated Base62-like codes, starting around 8 chars as a candidate; calculate collision probability and usability, enforce unique DB constraint + bounded retry. | M3 |
| OQ-06 | Custom alias and expiry in MVP? | Keep custom alias and expiry as optional P1 unless they materially strengthen demo scope without slowing core. | M0/M3 |
| OQ-07 | Minimum click metadata? | Timestamp, link ID, coarse device/browser and sanitized referrer are candidate fields; no raw IP by default. Avoid geo enrichment/API. | M4 |
| OQ-08 | Analytics raw-event retention? | Decide explicit retention and deletion policy; consider aggregates after raw event retention. | M4/M6 |
| OQ-09 | Analytics delivery guarantee? | Baseline at-least-once with idempotent consumer and documented publisher failure window. Evaluate transactional outbox only if required. | M4 |
| OQ-10 | Abuse workflow before exposure? | Define report mechanism, operator/admin identity, disablement and audit event. Keep deployment local until it exists. | Before public demo |
| OQ-11 | How will a demo be shared at zero cost? | Local live demo, screen recording and screenshots are always available. Check current free hosting options only if owner later allows a hosted service and accepts terms/limits. | M7 |
| OQ-12 | Is Pulse the final public name? | Use Pulse as working name; perform name/domain/trademark check before public branding. | Before public release |

## Decision log

| Date | Question | Decision | Rationale | Revisit condition |
|---|---|---|---|---|
| 31 Oct 2026 | Cloud runtime | No AWS/cloud dependency in baseline | Strict zero-cost constraint; AWS not needed to demonstrate core backend/system-design skills | Owner explicitly approves a cost-bounded cloud experiment |
| 31 Oct 2026 | Baseline architecture | Next.js + TS, Express + TS, PostgreSQL, Drizzle, Zod, Redis, RabbitMQ, worker, Docker Compose | Covers required skills with local open-source infrastructure | Evidence shows complexity or compatibility issue |
| 31 Oct 2026 | Service decomposition | Modular monolith + separate worker | Clear boundaries without premature microservices | Independently scaling/operating a module becomes necessary and evidence-backed |
