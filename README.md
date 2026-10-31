# Pulse

**Pulse** is a local-first, high-performance URL shortener and link analytics platform built to demonstrate serious full-stack and backend engineering: TypeScript, Next.js, API design, PostgreSQL, Redis, asynchronous processing, Docker, security, observability, and measured performance engineering.

> Project status: **M0 — product and architecture foundation**. This repository currently contains planning, architecture, security, and agent-operating documentation. Application code has not started.

## Start here

1. Read [`AGENTS.md`](AGENTS.md) for the always-on project guardrails.
2. Read [`docs/ANTIGRAVITY-HANDOFF.md`](docs/ANTIGRAVITY-HANDOFF.md) for the full project context and working agreement.
3. Read [`docs/PRD.md`](docs/PRD.md), [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md), and [`docs/SECURITY-CHECKLIST.md`](docs/SECURITY-CHECKLIST.md).
4. Review [`docs/milestones/M0/M0-CHECKLIST.md`](docs/milestones/M0/M0-CHECKLIST.md) and [`docs/memory/NEXT-SESSION.md`](docs/memory/NEXT-SESSION.md).

## Product in one paragraph

A user signs in, creates and manages short links, shares a short URL, and views click analytics in a clean dashboard. A public redirect endpoint resolves a short code quickly using Redis as a cache in front of PostgreSQL. Redirect events are published to RabbitMQ and processed by an independent worker so analytics work does not sit in the critical redirect path. The first version is a modular monolith with a separately runnable worker, not a microservices platform.

## Baseline technology direction

| Area | Baseline | Purpose |
|---|---|---|
| Web application | Next.js + TypeScript | Product UI, dashboard, account-facing experience |
| API / redirect service | Node.js + Express 5 + TypeScript | Explicit backend service and redirect path |
| Database | PostgreSQL | Durable source of truth and relational integrity |
| ORM / query layer | Drizzle ORM | Typed SQL access and migrations |
| Validation | Zod | Runtime validation at trust boundaries |
| Cache / rate-limit store | Redis | Hot redirect lookups and distributed rate-limit state |
| Message broker | RabbitMQ | Asynchronous click-event delivery |
| Background processing | Separate TypeScript worker process | Consume and aggregate analytics events |
| Local infrastructure | Docker + Docker Compose | Reproducible multi-container development |
| Testing | Vitest, Supertest, Playwright, k6 | Unit/integration, API, browser and load testing |
| CI | GitHub Actions | Repeatable quality gates |
| Design | Figma Education / Figma Make | UX exploration and design handoff |

The baseline is local-first and designed to run without paid cloud services. AWS is **not** a runtime dependency. The user's AWS Cloud Practitioner certification may be represented honestly as foundational knowledge; Pulse must not claim an AWS deployment that does not exist.

## Current milestone map

- **M0 — Foundation:** product definition, architecture baseline, security model, ADRs, agent workflow, acceptance criteria.
- **M1 — Repository and runtime foundation:** application skeleton, TypeScript/tooling, Docker Compose, health checks, CI, configuration validation.
- **M2 — Identity and link management:** account/session flow and owner-scoped link CRUD.
- **M3 — Redirect hot path:** secure short-code resolution, PostgreSQL fallback, Redis caching and invalidation.
- **M4 — Analytics pipeline:** event schema, RabbitMQ publisher/consumer, idempotency, aggregation and failure behaviour.
- **M5 — Product dashboard:** create/manage links and explore analytics in a polished interface.
- **M6 — Security and reliability hardening:** threat-driven tests, abuse controls, observability and failure experiments.
- **M7 — Performance and portfolio release:** reproducible k6 tests, bottleneck analysis, documentation and demo readiness.

Milestones are planning boundaries, not permission to implement everything in one large change. See [`docs/ROADMAP.md`](docs/ROADMAP.md).

## Non-negotiable project principles

- No paid cloud, database, messaging, monitoring, domain, or API dependency without explicit approval.
- No AWS deployment claims unless it is actually deployed and verified.
- Keep the first architecture intentionally small; do not introduce microservices, Kubernetes, Kafka, ClickHouse, multi-region infrastructure, or billing as MVP requirements.
- Treat security, tests, observability, and documentation as part of each feature—not cleanup work at the end.
- Measure performance and publish only reproducible results with environment and methodology.
- AI agents propose and implement; the human project owner approves meaningful architecture changes.

## Documentation map

- `docs/ANTIGRAVITY-HANDOFF.md` — comprehensive context and agent collaboration contract.
- `docs/PRD.md` — initial product requirements.
- `docs/ARCHITECTURE.md` — system shape, request flows, data boundaries and operational model.
- `docs/SECURITY-CHECKLIST.md` — threat-driven controls and evidence checklist.
- `docs/ROADMAP.md` — milestone sequence and exit criteria.
- `docs/decisions/` — Architecture Decision Records (ADRs).
- `docs/milestones/M0/` — M0 outputs and completion checklist.
- `docs/engineering-log/` — durable bug/experiment records.
- `docs/memory/` — concise project memory and next-session handoff.

## Status honesty

At M0, no endpoint, schema, deployment, security control, performance number, or automated test is claimed as implemented. The documentation describes intended decisions and the evidence required before calling them complete.
