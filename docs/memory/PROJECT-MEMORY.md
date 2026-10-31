# Pulse — compact project memory

## Product

Pulse is a polished URL shortener and link analytics service. Users create/manage short links, visitors redirect through public short-code resolution, and click analytics are processed asynchronously. Custom aliases and link expiry are deferred to P1.

## Owner goals

Build an interview-grade full-stack/backend project demonstrating TypeScript, Next.js, Docker, APIs, PostgreSQL, Redis, queues, security, reliability, observability and measured performance. Owner is learning the stack and expects agents to teach concepts, proactively catch issues and document trade-offs. Owner approves meaningful architecture changes.

## Cost and cloud

Zero-cost/local-first hard constraint. No AWS runtime dependency, paid hosting, managed data stores, paid APIs or domains without explicit approval. No public deployment until abuse controls (report, disable, audit trail) are implemented and verified. All development container ports bound strictly to `127.0.0.1`.

## Approved baseline

Next.js 16.4.3 + React 19.3.1 + TypeScript; Node.js 24.x Maintenance LTS (Node 26 Active LTS alternative documented) + Express 5.3.x + TypeScript; PostgreSQL 18.6-alpine (`pgdata:/var/lib/postgresql`); Drizzle ORM 0.45.x / Drizzle Kit 0.31.x; Zod (evaluating 4.x / 3.24.x peer matrix); Redis 8.10.3-alpine for cache and rate-limit state (password authenticated); RabbitMQ 4.4.0-management-alpine for asynchronous analytics (confirmed over BullMQ); separate TypeScript worker; Docker Compose (infrastructure only); Vitest 5.x; Supertest; Playwright (scaffolded; browser download deferred); GitHub Actions (full commit SHAs); ESLint 10 flat config (`eslint.config.mjs`); Dependabot (npm, github-actions, docker); Figma Education/Figma Make for design.

## Architecture stance

Modular monolith plus separate worker. Apps run on host via pnpm; infrastructure runs in Docker. PostgreSQL is source of truth. Redis is disposable cache. RabbitMQ is analytics event broker (validated at M0 review). Analytics reliability contract: best-effort bounded publication (timeouts are indeterminate; pre-broker loss accepted), at-least-once processing of confirmed messages with idempotent consumer and dead-letter routing. No transactional outbox in MVP. Redirect availability takes absolute priority over analytics availability. Do not fetch destination URLs server-side in MVP.

## Workspace structure

`apps/web`, `apps/api`, `apps/worker` for runnable applications. `packages/database`, `packages/shared`, `packages/config` for shared libraries. pnpm workspaces (exact version pinned in `packageManager`); no Turborepo.

## Key M0 decisions (1 Nov 2026)

- RabbitMQ confirmed over BullMQ (ADR-004 approved).
- Analytics contract refined: best-effort bounded publish, indeterminate timeouts, at-least-once post-confirmation, DLQ routing (ADR-006 approved).
- Next.js proxy rewrite for dev-time API calls; redirects directly through Express. No `cors` middleware initially.
- Express 5.3; Node 24 Maintenance LTS; Next 16.4.3; React 19.3.1; PG 18.6; Redis 8.10.3; RabbitMQ 4.4.0.
- Native `crypto.randomUUID()` used; no Nano ID.
- Helmet baseline security headers in Express API.
- `/api/v1` prefix for API versioning.
- Custom alias and link expiry deferred to P1.
- P0 scope accepted as drafted. Local-only until abuse workflow exists.
- Container ports strictly bound to `127.0.0.1`; credentials required from `.env`.
- Auth direction: server-side cookie sessions, PostgreSQL-backed store (full ADR at M2).

## Development workflow

Understand → plan → approve → implement → test → independent review → document → handoff. Small scoped changes. Strict TypeScript. Zod at trust boundaries. No secrets in code. No fabricated test results. Regression tests for bugs. ADRs for significant decisions. Engineering log for non-trivial bugs/experiments. Keep current status and next-session notes updated.

## Tool roles

- Antigravity IDE (enhanced with MCP + Skills) is the primary coding environment. Use model choice based on task and quota; run independent review with another model when feasible.
- ChatGPT is the architecture/continuity/tutoring coordinator; checked-in docs remain canonical memory.
- Figma Education / Figma Make is used for UI/UX exploration and approved design handoff.
- Claude native models and the owner-reported Claude skills are optional review/design/testing aids; do not assume they are installed in Antigravity.
- OmniRoute is optional only; owner currently has no Claude Code access.

## Current state

M0 documentation sign-off approved (1 Nov 2026). M1 implementation plan prepared (`docs/milestones/M1/M1-PLAN.md`); implementation authorization is pending owner approval. No app code exists. See `CURRENT-STATUS.md` and `NEXT-SESSION.md`.
