# Pulse — roadmap and milestone exit criteria

**Status:** Initial roadmap (M0)
**Rule:** A milestone may be re-scoped with an ADR/owner approval. Do not interpret this roadmap as permission to build future stages early.

## M0 — Product and architecture foundation

**Goal:** Agree what Pulse is, how it will be built, what will not be built, and how AI-assisted engineering will be governed.

Deliverables:
- PRD and user journeys.
- Architecture baseline and request-flow diagrams.
- Zero-cost boundary and approved stack.
- Security checklist and M0 threat register.
- ADRs for baseline technology and high-impact choices.
- Antigravity handoff, workspace instructions and implementation workflow.
- M1 task plan, open questions and project memory.

Exit criteria: owner reviews/approves the baseline, unresolved choices are resolved or explicitly deferred, and the M1 plan is ready. No application endpoint is required in M0.

## M1 — Repository and runtime foundation

**Goal:** Create a clean, reproducible developer environment before product logic.

Candidate work:
- Confirm repository structure and package/workspace strategy.
- Pin current supported Node.js/package versions after checking official release status.
- Bootstrap Next.js and Express TypeScript apps plus worker package/process.
- Configure strict TypeScript, formatting, linting and environment validation.
- Add Dockerfiles and Docker Compose for PostgreSQL, Redis and RabbitMQ with health checks, private networking and persistent local volumes.
- Add database migration workflow and minimal initial health/readiness endpoints.
- Add Vitest/Supertest test harness and initial Playwright setup.
- Add GitHub Actions for install, lint, typecheck, tests and build.
- Add structured logging and request correlation ID foundation.

Exit criteria:
- New checkout can be started from documented commands.
- Containers report healthy and application processes wait for dependencies safely.
- Secrets are not committed; `.env.example` is safe and complete.
- CI checks run from a clean checkout.
- README accurately describes setup and known gaps.

## M2 — Identity and link management

**Goal:** Implement secure account/session handling and owner-scoped link CRUD.

Candidate work:
- Finalize auth/session ADR and password hashing/session lifecycle policy.
- Add user and link migrations with constraints/indexes.
- Implement registration, login, logout and session validation.
- Implement link create/list/detail/update/disable/enable/delete.
- Add Zod request validation and consistent error contracts.
- Add rate limits to auth and link creation/mutation routes.
- Add owner-isolation tests for every private resource endpoint.

Exit criteria:
- Cross-user access is denied and regression-tested.
- Input boundaries, session lifecycle and rate limits are tested.
- Migrations work from an empty DB and are documented.

## M3 — Redirect hot path and cache

**Goal:** Implement fast, safe and correct public short-code resolution.

Candidate work:
- Finalize short-code generation ADR.
- Generate cryptographically random codes and rely on DB uniqueness plus bounded collision retry.
- Implement redirect lookup with Redis cache-aside and PostgreSQL fallback.
- Implement disabled/expired/not-found handling.
- Implement cache invalidation on link lifecycle changes and bounded negative caching.
- Decide redirect HTTP status semantics.
- Add redirect-specific validation, observability and rate-limit policy.

Exit criteria:
- Happy path and invalid-code tests pass.
- Cache hit/miss/fallback and invalidation tests pass.
- Stale-cache window is documented and verified.
- Redis-down test demonstrates the intended fallback.

## M4 — Analytics event pipeline

**Goal:** Move click processing out of the redirect critical path and make it resilient to duplicates/retries.

Candidate work:
- Approve versioned click-event schema and privacy/retention model.
- Configure durable RabbitMQ topology and publisher-confirm strategy.
- Publish event with stable ID and bounded publish behaviour.
- Implement worker validation, idempotency, acknowledgement, retries and dead-letter handling.
- Persist raw/minimized events and/or aggregates in PostgreSQL.
- Add queue depth, publish failure, processing latency and DLQ observability.
- Test broker and worker failures, duplicates and poison messages.

Exit criteria:
- Redirect remains usable under tested broker/worker failure conditions.
- Duplicate delivery does not double-count.
- Retry/DLQ lifecycle is verified.
- Event-loss and eventual-consistency limitations are documented.

## M5 — Product dashboard and analytics UI

**Goal:** Deliver a polished end-to-end experience on top of verified APIs.

Candidate work:
- Finalize Figma design direction and component tokens.
- Build responsive dashboard, link list, create/edit flows and link detail view.
- Build analytics cards and time series with meaningful empty/partial/loading states.
- Add copy/share interaction and accessible form feedback.
- Add end-to-end Playwright flows.

Exit criteria:
- Main journeys work in desktop and mobile viewport tests.
- Accessibility basics and keyboard flows are reviewed.
- API remains the authorization authority; UI state is not trusted for permissions.

## M6 — Security, reliability and operational hardening

**Goal:** verify threats, operational failure modes and data handling beyond the happy path.

Candidate work:
- Close P0 security checklist items with evidence.
- Dependency/secret scans and manual threat review.
- Abuse reporting/takedown process and policy.
- Rate-limit and resource-exhaustion tests.
- Redis/PostgreSQL/RabbitMQ/worker failure experiments.
- Retention/deletion behaviour and audit events.
- Health/readiness, structured logs and local observability.

Exit criteria:
- Security release gate reviewed and signed off by owner.
- Known limitations and accepted risks are visible.
- Failure experiments have repeatable steps and documented results.

## M7 — Performance evaluation and portfolio release

**Goal:** demonstrate what the system can do and what its limits are with honest evidence.

Candidate work:
- Establish k6 scenarios: warm cache, cold cache, read-heavy mixed load, and degraded dependencies.
- Record machine specs, container limits, runtime versions, test data, VUs, duration, latency percentiles, error rate and throughput.
- Inspect PostgreSQL query plans, pool use, Redis hit rate and queue/worker throughput.
- Identify and fix measured bottlenecks; rerun tests.
- Publish architecture, security summary, ADRs, engineering lessons, benchmark methodology and known limitations.
- Prepare a short demo walkthrough and interview discussion guide.

Exit criteria:
- Results are reproducible and not inflated or fabricated.
- README reflects actual implemented features and test evidence.
- The owner can explain the design, trade-offs and failure modes.
