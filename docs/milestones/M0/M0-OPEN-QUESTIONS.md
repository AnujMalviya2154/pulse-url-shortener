# M0 — open questions for owner review

These are the remaining choices worth discussing before M1/M2. The agent must not silently decide them.

| ID | Question | Status | Current recommendation / options | Needed by |
|---|---|---|---|---|
| OQ-01 | RabbitMQ vs BullMQ + Redis? | **Decided** | RabbitMQ (4.4.0-management-alpine) confirmed as dedicated analytics broker. Cache and queue remain independent. | ~~M0 sign-off~~ |
| OQ-02 | What authentication model? | Open — directional preference recorded | Server-side cookie sessions with PostgreSQL-backed store. Redis remains cache/rate-limit state, not durable auth store. Full design and security review at M2. | M2 planning |
| OQ-03 | How will local API and web origins work? | **Decided** | Next.js proxy rewrites for `/api/v1/*` in development. Short-link redirects resolve directly through Express. Cross-origin/CORS deferred unless needed. | ~~M1/M2~~ |
| OQ-04 | Which redirect status? | Open | Temporary redirect while destinations are editable; assess 302 vs 307 semantics and browser/proxy caching. Avoid permanent caching until behaviour is clear. | M3 |
| OQ-05 | What short-code alphabet and length? | Open | CSPRNG-generated Base62-like codes, starting around 8 chars as a candidate; calculate collision probability and usability, enforce unique DB constraint + bounded retry. | M3 |
| OQ-06 | Custom alias and expiry in MVP? | **Deferred to P1** | Core MVP ships without custom aliases or link expiry. Both deferred to P1 — they add validation/lifecycle complexity without strengthening the core engineering demo. | ~~M0/M3~~ |
| OQ-07 | Minimum click metadata? | Open | Timestamp, link ID, coarse device/browser and sanitized referrer are candidate fields; no raw IP by default. Avoid geo enrichment/API. | M4 |
| OQ-08 | Analytics raw-event retention? | Open | Decide explicit retention and deletion policy; consider aggregates after raw event retention. | M4/M6 |
| OQ-09 | Analytics delivery guarantee? | **Decided** | Four-stage contract: best-effort bounded publication (timeouts are indeterminate; pre-broker loss accepted), at-least-once processing of confirmed messages with idempotent consumer and dead-letter routing. No transactional outbox in MVP. See ADR-006. | ~~M4~~ |
| OQ-10 | Abuse workflow before exposure? | Open | Define report mechanism, operator/admin identity, disablement and audit event. Keep deployment local until it exists. | Before public demo |
| OQ-11 | How will a demo be shared at zero cost? | Open | Local live demo, screen recording and screenshots are always available. Check current free hosting options only if owner later allows a hosted service and accepts terms/limits. | M7 |
| OQ-12 | Is Pulse the final public name? | Open | Use Pulse as working name; perform name/domain/trademark check before public branding. | Before public release |

## Directional preferences & M1 architectural agreements (1 Nov 2026)

| Topic | Decision / Direction | Target Milestone |
|---|---|---|
| Authentication | Server-side opaque sessions, PostgreSQL-backed session store, HttpOnly/SameSite cookies. Redis is not the durable auth store. | M2 (ADR + design) |
| Workspace structure | `apps/web`, `apps/api`, `apps/worker` for runnable apps; `packages/database`, `packages/shared`, `packages/config` for shared libraries. pnpm workspaces (exact version pinned in `packageManager`); no Turborepo. | M1 |
| API versioning | `/api/v1` prefix. | M1 |
| Host port binding | All Docker Compose published ports bound strictly to `127.0.0.1`. | M1 |
| Container credentials | All database, cache, and broker passwords externalized via required `.env` variables (`${VAR:?error}`). Authenticated Redis configured. All credentials development-only. | M1 |
| Execution model | Docker Compose runs infrastructure only (Postgres 18.6, Redis 8.10.3, RabbitMQ 4.4.0); apps run on host via pnpm. | M1 |
| Database volume | Named volume mounted to `pgdata:/var/lib/postgresql` conforming to PostgreSQL 18 layout. | M1 |
| Tooling & Standards | ESLint 10 flat config (`eslint.config.mjs`), Node.js 24 Maintenance LTS baseline (with Node 26 Active LTS documented), Next.js 16.4.3, Express 5.3, native `crypto.randomUUID()`, Helmet HTTP baseline headers. | M1 |
| Drizzle Migrations | Tooling configured in M1; zero empty migration files generated until M2 schema. | M1 |

## Decision log

| Date | Question | Decision | Rationale | Revisit condition |
|---|---|---|---|---|
| 31 Oct 2026 | Cloud runtime | No AWS/cloud dependency in baseline | Strict zero-cost constraint; AWS not needed to demonstrate core backend/system-design skills | Owner explicitly approves a cost-bounded cloud experiment |
| 31 Oct 2026 | Baseline architecture | Next.js + TS, Express + TS, PostgreSQL, Drizzle, Zod, Redis, RabbitMQ, worker, Docker Compose | Covers required skills with local open-source infrastructure | Evidence shows complexity or compatibility issue |
| 31 Oct 2026 | Service decomposition | Modular monolith + separate worker | Clear boundaries without premature microservices | Independently scaling/operating a module becomes necessary and evidence-backed |
| 1 Nov 2026 | OQ-01 — Message broker | RabbitMQ confirmed over BullMQ + Redis | Separation of cache and broker concerns; stronger interview/learning value for dedicated broker concepts; acceptable operational overhead with Docker Compose | RabbitMQ setup materially delays the project; compare with real evidence |
| 1 Nov 2026 | OQ-03 — Origin strategy | Next.js proxy rewrite for dev; redirects directly through Express | Avoids CORS/cookie cross-origin complexity during development; redirect path stays on Express without Next.js overhead | Production deployment or security review requires explicit CORS/reverse proxy |
| 1 Nov 2026 | OQ-06 — Custom alias and expiry | Deferred to P1 | Core MVP is complete without them; adding them later requires no architectural changes; reduces MVP scope and risk | Owner decides they are needed for the engineering demo |
| 1 Nov 2026 | OQ-09 — Analytics delivery | Best-effort bounded publication, indeterminate timeouts, at-least-once processing of confirmed messages, DLQ routing | Redirect availability takes absolute priority; timeouts do not prove event drop; outbox deferred; poison pills terminate in DLQ | Measured event loss or timeout rate is unacceptable for the analytics product |
| 1 Nov 2026 | P0/P1 scope | Existing PRD P0 scope accepted without expansion | FR-13 (alias) and FR-14 (expiry) confirmed P1; FR-15 (abuse) P0 before public exposure | — |
| 1 Nov 2026 | Risk posture | Local-only deployment until abuse controls exist | No public exposure without reporting, operator disable, audit trail and baseline abuse controls | Abuse workflow implemented and verified |
| 1 Nov 2026 | Infrastructure versions | PG 18.6-alpine, Redis 8.10.3-alpine, RabbitMQ 4.4.0-management-alpine, Node 24 Maintenance LTS, Next.js 16.4.3, exact pinned pnpm | Current stable releases; exact patches to be verified prior to install; Node 24 retained for compatibility | Compatibility or stability defect identified |
| 1 Nov 2026 | Port binding security | Bind container ports strictly to `127.0.0.1` | Prevents local network exposure of development data stores | Multi-device LAN testing required |
