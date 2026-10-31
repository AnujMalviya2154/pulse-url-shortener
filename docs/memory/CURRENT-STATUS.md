# Pulse — current status

**As of:** 1 November 2026
**Current milestone:** M0 sign-off complete; M1 implementation authorization pending
**Implementation state:** Documentation-only. No application source code, migrations, endpoints, containers or deployment have been implemented.

## Baseline direction (approved 1 November 2026)

- Web: Next.js 16.4.3 + React 19.3.1 + TypeScript
- API/redirect: Node.js 24.x Maintenance LTS (Node 26 Active LTS documented) + Express 5.3.x + TypeScript
- Data: PostgreSQL 18.6-alpine (`pgdata:/var/lib/postgresql`) + Drizzle ORM 0.45.x / Drizzle Kit 0.31.x
- Validation: Zod (evaluating 4.x / 3.24.x peer matrix)
- Cache/rate limit state: Redis 8.10.3-alpine (password authenticated)
- Async click events: RabbitMQ 4.4.0-management-alpine + separate TypeScript worker
- Local infrastructure: Docker Compose (PostgreSQL, Redis, RabbitMQ bound to `127.0.0.1` with required `.env` interpolation)
- Package manager: pnpm workspaces (exact version pinned in `packageManager`)
- Tests: Vitest 5.x, Supertest, Playwright (deferred binaries), k6
- CI: GitHub Actions with verified full-length commit SHAs and `permissions: contents: read`; Dependabot for npm, github-actions, and docker
- Tooling: ESLint 10 flat configuration (`eslint.config.mjs`), Helmet HTTP baseline security headers
- Design: Figma Education / Figma Make
- Hosting: local-first; no AWS or paid runtime dependency; no public deployment until abuse controls exist

## M0 review state (1 November 2026)

Approved decisions:
- RabbitMQ confirmed as dedicated broker (ADR-004 approved).
- Analytics contract refined: best-effort bounded publication, indeterminate timeouts, at-least-once post-confirmation processing, idempotent consumer, DLQ routing (ADR-006 approved).
- Next.js proxy rewrite for development; redirects directly through Express. No `cors` middleware initially.
- Express 5.3 confirmed as current stable major release.
- `/api/v1` API prefix.
- `apps/{web,api,worker}` + `packages/{database,shared,config}` workspace structure with pnpm.
- Strict `127.0.0.1` binding for published container ports; required credentials from `.env`.
- Native `crypto.randomUUID()` adopted; `cors`, `nanoid`, `concurrently` pruned.
- Helmet baseline security headers in Express API.
- Drizzle tooling configured without empty migrations.
- Custom alias and link expiry deferred to P1.
- P0 scope accepted. Local-only deployment until abuse workflow verified.
- Auth direction: server-side cookie sessions, PostgreSQL-backed store (full ADR at M2).

Sign-off status:
- M0 documentation sign-off: **Approved by project owner (1 November 2026)**.
- M1 implementation plan: **Prepared in `docs/milestones/M1/M1-PLAN.md` (Implementation authorization pending)**.

Deferred decisions:
- OQ-02 (auth details) → M2
- OQ-04 (redirect status) → M3
- OQ-05 (short-code length) → M3
- OQ-07/08 (analytics metadata/retention) → M4/M6
- OQ-10 (abuse workflow) → pre-demo
- OQ-11/12 (demo sharing, naming) → M7/pre-release

## Repository

Published to GitHub. Branch: `main`. Clean working tree. Workspace instructions (`AGENTS.md`, `.agents/rules/implementation-workflow.md`) confirmed active in Antigravity.

## Next step

Owner authorization of M1 implementation before writing any code.

## Honesty note

No tests have been run because no application code exists yet. No performance, security or deployment claims are made.
