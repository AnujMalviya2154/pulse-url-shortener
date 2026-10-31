# M1 — Repository and runtime foundation

**Status:** Proposed — owner direction agreed; M1 implementation authorization pending
**Goal:** Create a clean, reproducible developer environment and verified infrastructure baseline before writing any product logic.
**Prerequisite:** M0 documentation sign-off approved (1 Nov 2026).

---

## 1. Scope Boundaries

### In Scope

- **Root workspace configuration:** pnpm workspaces with exact pinned version in `package.json` `packageManager`.
- **Application skeletons:**
  - `apps/api`: Node.js 24 Maintenance LTS + Express 5.3.x + strict TypeScript + Helmet.
  - `apps/web`: Next.js 16.4.3 + React 19.3.1 + strict TypeScript.
  - `apps/worker`: Node.js 24 Maintenance LTS + strict TypeScript.
- **Shared packages:**
  - `packages/config`: Shared TypeScript configs (bounded `< 6.1` for `typescript-eslint`), ESLint 10 flat config, Prettier.
  - `packages/shared`: Shared types, runtime environment validation schemas (Zod).
  - `packages/database`: Drizzle ORM 0.45.x configuration, database connection client export, migration CLI scripts (no empty migration generated in M1).
- **Docker Compose infrastructure:**
  - PostgreSQL 18.6-alpine (volume mounted to `pgdata:/var/lib/postgresql`).
  - Redis 8.10.3-alpine (password authenticated).
  - RabbitMQ 4.4.0-management-alpine.
  - All host port bindings strictly pinned to loopback (`127.0.0.1`).
  - All credentials externalized via required `.env` variables (`${VAR:?error}`); no hardcoded secrets in YAML.
  - Container health checks (including authenticated Redis check), internal bridge network (`pulse-net`), and persistent data volumes.
- **Execution model:**
  - Docker Compose runs infrastructure services only.
  - Apps (`api`, `web`, `worker`) run on host via pnpm workspace scripts.
- **API baseline:**
  - `/health`: Process liveness endpoint (reports 200 if Node event loop is active).
  - `/ready`: Startup readiness endpoint (reports 200 once server has bound and environment is verified; no downstream dependency checks until client connections exist in M2+).
  - Correlation ID injection using native Node.js `crypto.randomUUID()`.
  - Structured JSON logging (Pino).
  - Baseline HTTP security headers via Helmet.
  - Global error handler leveraging Express 5 native async error propagation.
- **Next.js integration:**
  - Rewrites in `next.config.ts` proxying browser calls from `/api/v1/:path*` to `http://127.0.0.1:4000/api/v1/:path*`.
- **Testing harness:**
  - Vitest 5.x configured across packages.
  - Supertest configured against Express app.
  - Automated tests covering `/health`, `/ready`, and baseline security headers.
  - Playwright configuration file scaffolded (browser binary download and E2E suites deferred to M5).
- **CI pipeline & security:**
  - GitHub Actions workflow (`.github/workflows/ci.yml`).
  - Full-length 40-character commit SHAs verified before workflow creation (placeholders retained in plan).
  - Least-privilege permissions: `permissions: contents: read`.
  - Multi-ecosystem Dependabot configuration (`npm`, `github-actions`, `docker`).
- **Code quality:**
  - Strict TypeScript (`strict: true`, `noImplicitAny: true`, `strictNullChecks: true`).
  - ESLint 10 flat configuration (`eslint.config.mjs`).
  - Prettier for consistent formatting.
- **Documentation:**
  - Comprehensive local setup runbook in `README.md` with Windows PowerShell instructions.

### Not in Scope

- User authentication, session management, or database session store (M2).
- Link schema, URL shortening logic, or CRUD endpoints (M2).
- Redirect hot path or Redis caching integration (M3).
- RabbitMQ client connection, channel setup, or event publishing (M4).
- Analytics consumer processing or dead-letter queue handlers (M4).
- Frontend UI dashboard components or styling libraries (M5).
- App containerization or production Dockerfiles (M6/M7).
- k6 load tests (M7).
- Public cloud deployment or production reverse proxy.

---

## 2. Workspace File Structure

```text
pulse/
├── apps/
│   ├── api/                              # Express 5.3 backend
│   │   ├── src/
│   │   │   ├── index.ts                  # Bootstrapping & server listen
│   │   │   ├── app.ts                    # Express app definition, Helmet & middleware
│   │   │   ├── routes/
│   │   │   │   └── health.ts             # /health (liveness) & /ready (startup readiness)
│   │   │   └── middleware/
│   │   │       ├── request-id.ts         # crypto.randomUUID() correlation ID
│   │   │       ├── request-logger.ts     # Pino structured logger
│   │   │       └── error-handler.ts      # Express 5 async error handler
│   │   ├── tests/
│   │   │   ├── health.test.ts            # Supertest API health tests
│   │   │   └── security-headers.test.ts  # Supertest Helmet header tests
│   │   ├── package.json
│   │   └── tsconfig.json
│   ├── web/                              # Next.js 16.4.3 frontend
│   │   ├── src/
│   │   │   └── app/
│   │   │       ├── layout.tsx            # Root layout
│   │   │       └── page.tsx              # Minimal placeholder landing page
│   │   ├── next.config.ts                # Proxy rewrites for /api/v1/*
│   │   ├── package.json
│   │   └── tsconfig.json
│   └── worker/                           # Analytics background worker skeleton
│       ├── src/
│       │   └── index.ts                  # Bootstrapping & graceful SIGINT/SIGTERM handlers
│       ├── package.json
│       └── tsconfig.json
├── packages/
│   ├── config/                           # Shared developer tooling configs
│   │   ├── eslint/
│   │   │   └── index.js                  # ESLint flat config definition
│   │   ├── tsconfig/
│   │   │   ├── base.json                 # Strict base compiler options
│   │   │   ├── node.json                 # Node 24 runtime target
│   │   │   └── next.json                 # Next.js React target
│   │   └── package.json
│   ├── shared/                           # Shared runtime code & validation
│   │   ├── src/
│   │   │   ├── env.ts                    # Zod environment schemas
│   │   │   └── index.ts
│   │   ├── package.json
│   │   └── tsconfig.json
│   └── database/                         # Persistence configuration
│       ├── src/
│       │   ├── client.ts                 # Drizzle + PG client factory
│       │   ├── schema/                   # Empty in M1; schemas added in M2
│       │   └── index.ts
│       ├── drizzle.config.ts             # Drizzle Kit CLI configuration
│       ├── package.json
│       └── tsconfig.json
├── .github/
│   ├── workflows/
│   │   └── ci.yml                        # Pinned SHA Actions workflow
│   └── dependabot.yml                    # Automated dependency vulnerability updates
├── docker-compose.yml                    # Postgres 18.6, Redis 8.10.3, RabbitMQ 4.4.0 on 127.0.0.1
├── .env.example                          # Safe placeholder template
├── pnpm-workspace.yaml                   # Workspace package mappings
├── package.json                          # Root workspace orchestrator (pinned packageManager)
├── tsconfig.json                         # Project references / base tsconfig
├── eslint.config.mjs                     # Root ESLint 10 flat config
├── .prettierrc                           # Code formatting rules
├── .node-version                         # Node.js 24 LTS
├── .editorconfig                         # (existing)
├── .gitignore                            # (existing)
└── docs/                                 # (existing project documentation)
```

---

## 3. Dependency Matrix & Compatibility Verification Strategy

### Runtime Support Policy
- **Node.js 24 Maintenance LTS (Baseline):** Pinned in `.node-version` as the conservative, compatibility-first runtime to ensure stable ecosystem support across all dependencies.
- **Node.js 26 Active LTS (Documented Alternative):** Recognized as the modern active LTS. If during M1 installation all dependencies demonstrate flawless compatibility with Node 26 without warnings or build anomalies, a transition can be evaluated; otherwise Node 24 remains the locked baseline.
- **pnpm:** Exact stable version to be verified and pinned in `packageManager` in root `package.json`.

### Dependency Matrix (Target Candidates)

> **Peer Compatibility Verification Rule:** Exact versions will be verified together against the npm registry prior to running `pnpm install` in M1 to ensure complete peer dependency alignment. Upgrades will only be pinned if the combined installation, strict typecheck, and production build pass with zero peer dependency conflicts or warnings.

| Package | Evaluated Target | Target Workspace | Compatibility Rationale |
|---|---|---|---|
| `typescript` | `~6.0.0` (or `^5.9.x`) | Root dev | **Strict boundary:** `typescript-eslint` officially supports TypeScript `< 6.1`. TypeScript 7 is rejected to prevent AST parser incompatibilities. |
| `next` | `16.4.3` | `apps/web` | Current stable release with React 19 App Router and proxy rewrites. |
| `react`, `react-dom` | `19.3.1` | `apps/web` | Aligned with Next.js 16.4.3 peer dependency requirements. |
| `express` | `5.3.x` | `apps/api` | Native async route handlers; explicit backend redirect boundary. |
| `helmet` | `8.0.x` | `apps/api` | Standard baseline HTTP security headers. |
| `drizzle-orm` | `0.45.x` | `packages/database` | Type-safe SQL query generation. |
| `drizzle-kit` | `0.31.x` | `packages/database` dev | Schema management CLI (no empty migration generated in M1). |
| `pg` | `8.23.x` (or `8.13.x`) | `packages/database` | PostgreSQL client driver (evaluated latest compatible release). |
| `@types/pg` | `8.11.x` | `packages/database` dev | TypeScript definitions for `pg`. |
| `zod` | `4.x` (or `3.24.x`) | `packages/shared`, apps | Evaluate 4.x stability and peer compatibility with Drizzle ecosystem; fall back to 3.24.x if peer conflicts emerge. |
| `pino` | `10.4.x` (or `9.6.x`) | `apps/api`, `apps/worker` | Modern high-throughput structured JSON logging. |
| `pino-pretty` | `13.x` | Root dev | Local development log humanization. |
| `vitest` | `5.x` | Root dev | Fast unit and integration test runner. |
| `supertest` | `7.x` | `apps/api` dev | HTTP assertion library for Express routes. |
| `@types/supertest` | `7.x` | `apps/api` dev | Type definitions for Supertest. |
| `@playwright/test` | `1.64.x` | Root dev | Test harness configuration scaffolded; browser binaries deferred to M5. |
| `tsx` | `4.19.x` | Root dev | TypeScript execution engine for development scripts. |
| `eslint` | `10.x` | Root dev | Flat configuration (`eslint.config.mjs`) compatible with modern AST tools. |
| `prettier` | `3.9.x` (or `3.6.x`) | Root dev | Deterministic code formatting. |

### Explicitly Excluded Dependencies

- `cors`: Browser traffic proxied same-origin via Next.js rewrites.
- `nanoid`: Replaced with native Node.js `crypto.randomUUID()`.
- `concurrently`: Replaced with native pnpm parallel workspace execution (`pnpm --parallel -r run dev`).
- `turborepo`: Not required for initial modular monolith scale.

---

## 4. Docker Compose Specification (`docker-compose.yml`)

Security and layout controls:
- All host published ports bound strictly to `127.0.0.1` (loopback).
- All passwords externalized via `${VAR:?error}` syntax (fails fast if `.env` is unpopulated).
- PostgreSQL 18 volume mounted to `pgdata:/var/lib/postgresql`. Inside this directory, PostgreSQL 18 stores clusters under version-specific paths.
- Redis password authenticated via `--requirepass` and healthcheck authenticated via `-a`.
- All credentials clearly noted as development-only.

```yaml
services:
  postgres:
    image: postgres:18.6-alpine
    container_name: pulse-postgres
    ports:
      - "127.0.0.1:5432:5432"
    environment:
      POSTGRES_USER: ${POSTGRES_USER:-pulse_dev}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:?Error: POSTGRES_PASSWORD must be set in .env}
      POSTGRES_DB: ${POSTGRES_DB:-pulse_dev}
    volumes:
      # PostgreSQL 18 official image layout uses /var/lib/postgresql as volume root
      - pgdata:/var/lib/postgresql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER:-pulse_dev} -d ${POSTGRES_DB:-pulse_dev}"]
      interval: 5s
      timeout: 3s
      retries: 5
    networks:
      - pulse-net
    restart: unless-stopped

  redis:
    image: redis:8.10.3-alpine
    container_name: pulse-redis
    ports:
      - "127.0.0.1:6379:6379"
    command: ["redis-server", "--requirepass", "${REDIS_PASSWORD:?Error: REDIS_PASSWORD must be set in .env}"]
    volumes:
      - redisdata:/data
    healthcheck:
      test: ["CMD", "redis-cli", "-a", "${REDIS_PASSWORD:?Error: REDIS_PASSWORD must be set in .env}", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5
    networks:
      - pulse-net
    restart: unless-stopped

  rabbitmq:
    image: rabbitmq:4.4.0-management-alpine
    container_name: pulse-rabbitmq
    ports:
      - "127.0.0.1:5672:5672"     # AMQP protocol
      - "127.0.0.1:15672:15672"   # Management UI
    environment:
      RABBITMQ_DEFAULT_USER: ${RABBITMQ_DEFAULT_USER:-pulse_dev}
      RABBITMQ_DEFAULT_PASS: ${RABBITMQ_DEFAULT_PASS:?Error: RABBITMQ_DEFAULT_PASS must be set in .env}
    volumes:
      - rabbitmqdata:/var/lib/rabbitmq
    healthcheck:
      test: ["CMD", "rabbitmq-diagnostics", "-q", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - pulse-net
    restart: unless-stopped

volumes:
  pgdata:
    name: pulse_pgdata
  redisdata:
    name: pulse_redisdata
  rabbitmqdata:
    name: pulse_rabbitmqdata

networks:
  pulse-net:
    name: pulse-net
    driver: bridge
```

---

## 5. Local Setup Runbook (Windows PowerShell & Bash)

### 1. Initialize `.env` before invoking Docker Compose
Docker Compose uses `${VAR:?error}` interpolation. Running `docker compose up` without an active `.env` file will deliberately fail immediately.

```powershell
# Windows PowerShell
Copy-Item .env.example .env
```

```bash
# macOS / Linux Bash
cp .env.example .env
```

### 2. Synchronize credentials and connection URLs
Verify that composite URLs match their corresponding individual variables in `.env`:
- `DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@127.0.0.1:5432/${POSTGRES_DB}`
- `REDIS_URL=redis://:${REDIS_PASSWORD}@127.0.0.1:6379`
- `RABBITMQ_URL=amqp://${RABBITMQ_DEFAULT_USER}:${RABBITMQ_DEFAULT_PASS}@127.0.0.1:5672`

### 3. Git protection check
Verify that `.env` remains uncommitted and ignored by Git:
```powershell
git status --ignored
```

---

## 6. Express Baseline Security & Helmet Integration

In `apps/api/src/app.ts`:
```typescript
import express from 'express';
import helmet from 'helmet';

export const createApp = () => {
  const app = express();

  // Baseline HTTP security headers
  app.use(
    helmet({
      // HSTS enabled based on NODE_ENV; browsers honor it only when received over HTTPS.
      // Actual HTTPS enforcement belongs at the ingress / reverse proxy layer.
      hsts: process.env.NODE_ENV === 'production',
      // API serves raw JSON; content-security-policy is disabled here.
      // Frontend CSP is deferred to the Next.js application milestone (M5).
      contentSecurityPolicy: false,
    })
  );

  // ... routes and middleware ...
  return app;
};
```

### Verification Suite (`apps/api/tests/security-headers.test.ts`)
The Supertest integration test verifies:
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: SAMEORIGIN`
- `X-DNS-Prefetch-Control: off`
- Confirms HSTS (`Strict-Transport-Security`) is appropriately handled according to environment rather than failing local HTTP runs.

---

## 7. RabbitMQ Reliability & M4 Topology Roadmap

### Consumer Idempotency
Click events are generated with a cryptographically secure random `eventId` (UUIDv4 via `crypto.randomUUID()`). The PostgreSQL analytics schema enforces uniqueness:
```sql
CREATE TABLE click_events (
  id UUID PRIMARY KEY,
  link_id UUID NOT NULL REFERENCES links(id),
  occurred_at TIMESTAMPTZ NOT NULL,
  -- ... metadata ...
);
```
Idempotent insert:
```sql
INSERT INTO click_events (id, link_id, occurred_at)
VALUES ($1, $2, $3)
ON CONFLICT (id) DO NOTHING;
```
This guarantees protection against:
1. Broker at-least-once message redeliveries (`redelivered: true`).
2. Indeterminate publication timeouts where the message was accepted by the broker and delivered late.
3. Caller-side republications of the same stable `eventId`.

### Indeterminate Publication Timeouts
- A publication wait budget timeout (candidate 50ms to be calibrated in M4) represents an **unconfirmed outcome**, *not* a confirmed drop or confirmed duplicate.
- At timeout expiration, the Express API stops waiting and returns the HTTP 302/307 redirect to the visitor immediately.
- If the broker eventually persists and routes the message, the consumer processes it safely; if the message was never received by the broker, it is lost pre-broker. This preserves redirect availability as the non-negotiable priority.

### M4 Requeue, Bounded Retry & DLQ Architecture
1. **Requeue Placement:** `basic.nack(msg, false, true)` or `basic.reject(msg, true)` requeues the message at its original position where possible, otherwise closer to the queue head. It does **not** implement delayed exponential backoff by itself.
2. **Delayed Retries:** The concrete delayed retry strategy—choosing between native Quorum delayed retries (or delayed exchange plugins) vs. dead-letter exchanges with per-message TTL retry queues—is deferred to M4 implementation review.
3. **DLX Transfer Guarantees:** Delivery guarantees into the DLX depend on queue type (Classic vs. Quorum) and dead-letter strategy configuration (e.g. `dead-letter-strategy: at-least-once` on Quorum queues, which requires the `stream_queue` feature-flag prerequisite). We do **not** claim reliable DLQ transfer until the selected topology has been configured, benchmarked, and verified under failure injection in M4. Messages in the DLQ are terminal and excluded from product analytics unless reprocessed.
4. **Queue Type Review:** Classic Durable vs. Quorum Queues is deferred to M4 implementation review.

---

## 8. Modernized CI Specification (`.github/workflows/ci.yml`)

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

jobs:
  verify:
    name: Lint, Typecheck & Test
    runs-on: ubuntu-latest
    steps:
      # Evaluated actions/checkout@v7.0.1 (SHA verified prior to workflow file creation)
      - name: Checkout repository
        uses: actions/checkout@<verified-checkout-v7.0.1-commit-sha>

      # Evaluated pnpm/action-setup@v6.1.0 (SHA verified prior to workflow file creation)
      - name: Install exact pinned pnpm
        uses: pnpm/action-setup@<verified-action-setup-v6.1.0-commit-sha>
        with:
          # Reads exact pinned version from root package.json "packageManager" field
          run_install: false

      # Evaluated actions/setup-node@v7.1.0 (SHA verified prior to workflow file creation)
      - name: Setup Node.js
        uses: actions/setup-node@<verified-setup-node-v7.1.0-commit-sha>
        with:
          node-version-file: '.node-version'
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Run code formatting check
        run: pnpm format:check

      - name: Run linter
        run: pnpm lint

      - name: Run typecheck
        run: pnpm typecheck

      - name: Run unit & API health/security tests
        run: pnpm test

      - name: Build all packages & apps
        run: pnpm build
```

---

## 9. M1 Acceptance Criteria

Before M1 can be declared complete, every criterion below must be verified with documented terminal output:

- [ ] New checkout: Copying `.env.example` to `.env` (via PowerShell or Bash) and populating matching connection strings precedes Docker invocation.
- [ ] `docker compose up -d` starts PostgreSQL, Redis, and RabbitMQ; `docker compose ps` confirms all three are `healthy`.
- [ ] Published ports respond on `127.0.0.1` and are verified NOT accessible on external IP interfaces.
- [ ] PostgreSQL volume mount verifies data is preserved under `pgdata:/var/lib/postgresql`.
- [ ] Redis container requires authentication; unauthenticated `redis-cli ping` fails, authenticated ping succeeds.
- [ ] `pnpm install --frozen-lockfile` completes with zero dependency warnings or peer resolution failures using the exact pinned pnpm version.
- [ ] `pnpm typecheck` executes across all packages and apps with zero TypeScript errors under strict mode (TypeScript `< 6.1`).
- [ ] `pnpm lint` executes using ESLint 10 flat configuration (`eslint.config.mjs`) with zero errors or warnings.
- [ ] `pnpm format:check` passes across the repository.
- [ ] `apps/api` starts via `pnpm --filter @pulse/api dev` and responds `200` to `GET http://127.0.0.1:4000/health` and `GET http://127.0.0.1:4000/ready`.
- [ ] Automated Supertest suite confirms Helmet baseline security headers are present.
- [ ] `apps/web` starts via `pnpm --filter @pulse/web dev` and serves the home page at `http://localhost:3000`.
- [ ] Next.js proxy rewrite forwards a request from `http://localhost:3000/api/v1/health` to Express and returns `200 OK`.
- [ ] `apps/worker` starts via `pnpm --filter @pulse/worker dev`, logs startup in structured JSON, and exits cleanly (exit code 0) on `SIGINT`.
- [ ] `pnpm test` runs Vitest 5.x and executes the Supertest health/security suite with 100% passing results.
- [ ] `pnpm build` successfully produces production builds for `apps/api`, `apps/web`, and `apps/worker`.
- [ ] `.env.example` contains all required development variables with clear placeholder text; no `.env` files or secrets are tracked by Git.
- [ ] `packages/database` has migration tooling working, with zero dummy/empty SQL migration files present.
- [ ] GitHub Actions CI workflow passes on GitHub (or via local runner if offline) using verified full commit SHAs.
- [ ] `README.md` contains accurate setup instructions matching actual repository behavior.
