# M0 review record

- Review date: 1 November 2026
- Owner: Project owner (human)
- Agent/model(s) participating: Claude Opus 4.6 Thinking / Gemini 3.8 Flash (Antigravity IDE)
- Repository commit: 254d1c7 (main)
- M0 documentation sign-off: Approved by project owner (1 November 2026)
- M1 implementation authorization: Pending owner approval

## Product scope

- Approved P0 scope: FR-01 through FR-12; FR-15 (abuse controls) required before any public exposure.
- Deferred P1/P2 scope: FR-13 (custom alias), FR-14 (link expiry) deferred to P1. FR-16 (export) P2.
- Any PRD changes: Analytics reliability wording refined to distinguish best-effort bounded publication with indeterminate timeouts from at-least-once consumer processing. No scope additions.

## Architecture decisions

| Topic | Approved choice | Deferred/alternative | Rationale | ADR updated? |
|---|---|---|---|---|
| Runtime / cloud | Local-first, Docker Compose, no AWS | Cloud deployment deferred indefinitely | Zero-cost constraint | ADR-001 unchanged |
| API / web boundary | Express 5.3 API + Next.js proxy rewrite for dev | Production reverse proxy deferred | Direct Express control over redirect path; avoids CORS complexity during development | — |
| Database | PostgreSQL 18.6-alpine + Drizzle ORM | — | Relational integrity, typed migrations, visible SQL; pgdata:/var/lib/postgresql layout | ADR-003 unchanged |
| Cache / rate limiting | Redis 8.10.3-alpine (cache-aside + rate-limit state) | — | Disposable acceleration; password authenticated; not source of truth | ADR-004 status updated |
| Message broker | RabbitMQ 4.4.0-management-alpine (dedicated broker) | BullMQ rejected for MVP | Cache/queue separation; dedicated broker learning value | ADR-004 validated |
| Auth / sessions | Server-side sessions, PostgreSQL store (directional) | Full design deferred to M2 ADR | Revocable sessions; no JWT complexity; Redis not durable auth store | Pending M2 |
| Short-code generation | CSPRNG Base62 + DB uniqueness (proposed) | — | Unpredictable codes | ADR-005 unchanged; finalize at M3 |
| Analytics delivery | Best-effort bounded publish; indeterminate timeouts; at-least-once post-confirmation; idempotent consumer; DLQ routing | Transactional outbox deferred | Redirect availability > analytics availability; poison pills route to DLQ | ADR-006 updated |
| Workspace structure | apps/{web,api,worker} + packages/{database,shared,config}; pnpm 12 workspaces | No Turborepo | Lightweight monorepo; no extra tooling dependency | — |
| Host port binding | 127.0.0.1 loopback only | 0.0.0.0 rejected | Prevents exposure to local network | — |
| Compose credentials | Required .env interpolation (${VAR:?error}) | Hardcoded values rejected | Dev credential hygiene | — |
| API versioning | /api/v1 prefix | — | Simple, explicit, well-understood | — |

## Security and privacy

- Abuse/takedown approach: Deferred detailed design. Local-only deployment until abuse controls (report, disable, audit trail) are verified.
- Analytics fields allowed: Directional — timestamp, linkId, eventId, coarse device/browser, sanitized referrer. No raw IP. Finalize at M4.
- Retention/deletion approach: Deferred to M4/M6.
- Risks accepted or deferred: Pre-confirmation analytics event loss accepted (ADR-006). Cache staleness bounded by TTL. RabbitMQ complexity accepted with Docker Compose mitigation. No public exposure until abuse workflow exists. Port exposure mitigated via `127.0.0.1` binding.

## Open questions

- Closed: OQ-01 (RabbitMQ confirmed), OQ-03 (proxy rewrite), OQ-06 (alias/expiry → P1), OQ-09 (delivery contract refined)
- Deferred: OQ-02 → M2, OQ-04/05 → M3, OQ-07/08 → M4/M6, OQ-10 → pre-demo, OQ-11 → M7, OQ-12 → pre-release

## M1 approval

- M1 scope prepared: Yes (documented in docs/milestones/M1/M1-PLAN.md)
- M1 plan reference: docs/milestones/M1/M1-PLAN.md
- M1 implementation authorization: Pending owner approval
- Date: —
