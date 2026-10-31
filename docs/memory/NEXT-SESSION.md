# Next session — start here

**Current milestone:** M1 — Repository and runtime foundation
**Prerequisite:** M0 documentation sign-off completed and approved by owner. M1 implementation authorization pending.

## Session goal

Upon owner authorization, execute M1 implementation: pnpm workspaces with exact pinned version, strict TypeScript tooling, ESLint 10 flat config (`eslint.config.mjs`), Docker Compose infrastructure bound to `127.0.0.1` with required `.env` credentials, Express 5.3 API skeleton with Helmet, Next.js 16.4.3 skeleton, worker skeleton, process liveness (`/health`) and startup readiness (`/ready`) endpoints, Vitest 5.x + Supertest test harness, full-SHA GitHub Actions CI pipeline, and setup documentation.

## Before writing code

1. Confirm the agent has loaded `AGENTS.md` and `.agents/rules/implementation-workflow.md`.
2. Read `docs/milestones/M1/M1-PLAN.md` for approved scope, file structure and exit criteria.
3. Verify current exact patch versions and peer compatibility immediately before installing:
   - Node.js 24 Maintenance LTS baseline (with Node 26 Active LTS alternative documented)
   - Exact pnpm version to pin in `packageManager` (e.g. `pnpm@12.x.y`)
   - Next.js 16.4.3 / React 19.3.1
   - Express 5.3.x / Helmet 8.0.x
   - Drizzle ORM 0.45.x / Drizzle Kit 0.31.x
   - Zod 4.x / 3.24.x peer compatibility check
   - Vitest 5.x
   - ESLint 10.x
   - PostgreSQL 18.6-alpine (`pgdata:/var/lib/postgresql`)
   - Redis 8.10.3-alpine (password authenticated)
   - RabbitMQ 4.4.0-management-alpine
4. Do not start product features (link CRUD, auth, redirects) — M1 is infrastructure only.
5. Do not generate empty migration files in Drizzle.

## M1 implementation order

1. Root workspace: `pnpm-workspace.yaml`, root `package.json`, root `tsconfig.json`, `.node-version`.
2. Shared config package (`packages/config`): TypeScript base configs, ESLint flat config (`eslint.config.mjs`), Prettier.
3. Shared package (`packages/shared`): Zod env validation schemas, native `crypto.randomUUID()` utilities, common types.
4. Database package (`packages/database`): Drizzle config and CLI migration tooling (zero empty migration files).
5. API skeleton (`apps/api`): Express 5.3, Helmet baseline headers, `/health` (liveness), `/ready` (startup readiness), structured JSON logging, correlation IDs.
6. Web skeleton (`apps/web`): Next.js 16.4.3, proxy rewrite config for `/api/v1/*`, minimal page.
7. Worker skeleton (`apps/worker`): Startup log, graceful SIGINT/SIGTERM shutdown.
8. Local environment setup: Copy `.env.example` to `.env` (via PowerShell or Bash) and populate matching connection URLs before invoking Docker.
9. Docker Compose (`docker-compose.yml`): PostgreSQL 18.6, Redis 8.10.3, RabbitMQ 4.4.0 bound strictly to `127.0.0.1` with required `.env` interpolation and health checks.
10. Test infrastructure: Vitest 5.x, Supertest wired to Express, API health and security headers tests.
11. CI (`.github/workflows/ci.yml`): Verified full-length commit SHAs, `permissions: contents: read`, Dependabot for npm, actions, and docker.
12. Documentation: README setup runbook with Windows PowerShell instructions.

## Key constraints

- pnpm workspaces, no Turborepo. Exact pnpm version pinned.
- Express 5.3 with native async handlers and Helmet; Node 24 Maintenance LTS baseline.
- Docker services bound strictly to `127.0.0.1`; credentials required from `.env`.
- Native `crypto.randomUUID()`; no `cors`, no `nanoid`, no `concurrently`.
- ESLint flat configuration only (`eslint.config.mjs`).
- No database dependency checks in `/ready` until clients are implemented in M2+.
- No secrets committed; safe `.env.example`.

## M1 exit criteria

- `git clone` + `.env` setup + `docker compose up -d` + `pnpm dev` → working environment.
- Containers healthy on `127.0.0.1`.
- Express responds `200` to `/health` and `/ready` with expected security headers.
- Strict TypeScript compiles with zero errors.
- ESLint flat config passes with zero warnings.
- API health and security tests pass via `pnpm test`.
- CI succeeds on GitHub Actions with pinned SHAs.
- README accurately describes setup.
