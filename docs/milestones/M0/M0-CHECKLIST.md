# M0 completion checklist

## Product definition

- [x] Product summary and problem statement drafted.
- [x] Primary personas and jobs described.
- [x] Key user journeys documented.
- [x] Functional and non-functional requirements listed.
- [x] MVP non-goals identified.
- [x] Owner confirms P0/P1 scope. *(Approved 1 Nov 2026 — P0 accepted as drafted; FR-13/FR-14 confirmed P1)*

## Architecture

- [x] Modular monolith + separate worker proposed.
- [x] Next.js/TypeScript, Express/TypeScript, PostgreSQL, Drizzle, Zod, Redis, RabbitMQ, Docker Compose baseline documented.
- [x] Cache-aside and analytics event flow documented.
- [x] Failure behaviour and observability requirements documented.
- [x] Owner confirms remaining architecture questions. *(Approved 1 Nov 2026 — RabbitMQ confirmed; analytics reliability contract refined; loopback port binding, Express 5.3, Node 24 Maintenance LTS, Next 16.4.3, API versioning, workspace structure decided for M1)*

## Security

- [x] Initial threat register created.
- [x] URL redirect abuse and SSRF boundary explicitly documented.
- [x] IDOR/BOLA, CSRF/session, XSS, injection, rate limits, queue and data privacy risks listed.
- [x] Owner agrees MVP risk posture and abuse response process. *(Approved 1 Nov 2026 — local-only until abuse workflow exists; loopback container bindings enforced; risk posture accepted)*

## Agent operating model

- [x] Root Antigravity instructions created.
- [x] Modular Antigravity workflow rule created.
- [x] Plan/approve/implement/test/review/document workflow defined.
- [x] No-fabricated-evidence and security guardrails documented.
- [x] Confirm Antigravity actually loads the workspace instructions after opening the repo. *(1 Nov 2026 — verified: both AGENTS.md and .agents/rules/implementation-workflow.md are active)*

## Decisions and next stage

- [x] Initial ADRs written.
- [x] Open questions listed.
- [x] M1 candidate work and exit criteria listed.
- [x] Owner reviews and approves baseline. *(Approved 1 Nov 2026)*
- [x] M0 open questions are closed or deferred. *(Approved 1 Nov 2026 — OQ-01/03/06/09 decided; remaining deferred with milestone targets)*
- [x] M1 plan is prepared. *(docs/milestones/M1/M1-PLAN.md — M1 implementation authorization is pending)*

## M0 sign-off

- Owner approval date: 1 November 2026
- Approved by: Project owner
- Deferred decisions: OQ-02 (M2), OQ-04/05 (M3), OQ-07/08 (M4), OQ-10 (pre-demo), OQ-11/12 (M7/pre-release)
- M1 plan reference: `docs/milestones/M1/M1-PLAN.md` (Implementation authorization: Pending)
