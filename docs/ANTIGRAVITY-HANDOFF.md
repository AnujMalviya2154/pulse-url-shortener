# Pulse — full project context and Antigravity handoff

**Document status:** M0 baseline, 31 October 2026
**Purpose:** Give Antigravity (and any future coding/review agent) the full project context, decisions, constraints, expected behaviour and collaboration model so work can continue across sessions without relying on chat history.

---

## 1. What we are building

Pulse is a **high-performance URL shortener with link management and click analytics**. It should feel like a real, polished product while demonstrating deep backend and systems engineering. The point is not to collect as many technologies as possible. The point is to build a coherent system in which each component has a clear reason to exist, then demonstrate that the system is correct, secure, measurable and understandable.

A user will be able to:

1. Create an account and sign in securely.
2. Create a short link from a valid destination URL.
3. Optionally choose a custom alias if it is available and allowed.
4. View, search, edit, disable, enable and delete their own links.
5. Share a short URL that redirects visitors to the intended destination.
6. View click analytics for links they own: total clicks, time series and useful breakdowns such as referrer/device/browser where data quality supports them.
7. Report abuse or encounter a clearly disabled/expired-link response where appropriate.

The product should have an excellent visual hierarchy, responsive dashboard, polished empty/loading/error states and a coherent design system. Figma Education/Figma Make is the design exploration and handoff environment; Antigravity implements the approved design in code.

## 2. Why this project exists

Pulse is intended to be an interview-grade portfolio project and a structured learning experience. It must demonstrate:

- TypeScript used deliberately across frontend, API and worker code.
- Next.js application development and a clean UI architecture.
- Backend API design, authentication, authorization and validation.
- Relational data modelling, transactions, indexes and query analysis in PostgreSQL.
- Redis caching, cache invalidation, TTLs and graceful degradation.
- Asynchronous messaging, queues, consumers, acknowledgements, retries, idempotency and backpressure.
- Docker and Docker Compose for reproducible local infrastructure.
- Security engineering, threat modelling and abuse-aware design.
- Automated testing at unit, integration, API and browser levels.
- Observability, structured logs, health checks and failure diagnosis.
- Performance testing using k6, with real methodology and measured results.
- System design reasoning: bottlenecks, consistency, availability, failure isolation, data lifecycle and trade-offs.

The owner is learning parts of this stack while building. Explain concepts instead of treating architecture as a black box. Interview value comes from being able to explain why a component exists, what happens when it fails, what alternatives were rejected and what evidence supports performance claims.

## 2.1 AI toolchain and responsibility model

The owner has a multi-tool AI workflow. Treat this as context about the owner's available environment as reported on 31 October 2026; individual model availability, limits and names may change. **The repository and its checked-in documents are the canonical project memory**, not any single chat history.

### Antigravity IDE — primary implementation environment

Antigravity is the main coding/orchestration environment and is enhanced with MCP and a Skills framework; do not assume it is the stock/default setup. The owner reports access to these model choices inside Antigravity:

- Gemini 3.6 Flash — High / Medium / Low
- Gemini 3.5 Flash — High / Medium / Low
- Gemini 3.1 Pro — High / Low
- Claude Sonnet 4.6 Thinking
- Claude Opus 4.6 Thinking
- GPT-OSS 120B — Medium

The owner reports higher usage headroom for Gemini models than for Claude models in this environment. Use that as a practical constraint, not as a blanket claim that one model is always better. Select the model based on task complexity and available quota; keep the workflow model-agnostic.

**Suggested role allocation:**
- Use a fast/high-headroom model for routine, well-scoped implementation iterations, test scaffolding and documentation updates when it produces reliable results.
- Use a stronger reasoning model for architecture trade-offs, concurrency, data integrity, security reviews and difficult root-cause analysis.
- For important changes, request an independent review from a different model/agent when feasible. A second model's comments are hypotheses to verify with code, tests and evidence—not authority.
- Keep project rules in `AGENTS.md` and `.agents/rules/`; keep long-form product/architecture context in `docs/`. Do not depend on chat-only memory.

### Claude native environment — optional reviewer and specialist

The owner reports access to a separate Claude environment with model choices including Claude Opus 5, Opus 4.8, Opus 4.7, Fable 5, Sonnet 4.6, Sonnet 5, Opus 4.6 and Haiku 4.5. The owner reports more limited usage and longer waits here than in Antigravity. Treat this as an optional specialist/review environment, not a required dependency for day-to-day progress.

The owner also reports having Claude-side skills: **UI/UX Pro Max, Taste, WebTest, Impeccable, Memory and Find**. They can be useful for UI critique, browser-testing workflows, design polish and retrieval within that Claude environment. Do not assume these skills are installed or callable inside Antigravity unless the owner explicitly integrates them there.

### OmniRoute — optional routing experiment, not a project dependency

The owner has described OmniRoute routing profiles such as `auto/claude-sonnet`, `auto/claude-opus`, `auto/claude-fable` and `auto/claude-haiku`, with dynamic provider/model choices (including open-model routes). The owner currently does **not** have Claude Code access. Therefore, do not build the Pulse workflow around Claude Code, Cowork or OmniRoute. If access changes, the owner will say so; OmniRoute can then be evaluated as an optional way to obtain additional independent review, not as required infrastructure.

### Figma Education / Figma Make — design environment

The owner has a Figma Education plan with Figma Make and reports the following model options inside Figma: Default, Claude Sonnet 4.6, Claude Opus 4.8, Gemini 3.6 Flash, Gemini 3.1 Pro and GPT-5.6. Use Figma for exploration of information architecture, visual direction, dashboard layout and interaction prototypes. The owner approves the design direction; Antigravity then implements the approved screens and states. Do not treat a generated prototype as production-ready UI code without inspecting responsiveness, accessibility, component structure and state behaviour.

### ChatGPT — architecture coordination, continuity and tutoring

The owner considers ChatGPT a reliable place for long-running project coordination and wants it to help maintain architecture consistency, reason through trade-offs, prepare documentation, challenge assumptions, explain concepts and support interview preparation. ChatGPT should use checked-in project files as the source of truth and update them when a durable decision changes. Do not assume chat memory alone is sufficient.

### End-to-end tool workflow

1. **Product/architecture:** discuss requirements and options with the owner; record approved choices in PRD/architecture/ADRs.
2. **UI/UX:** explore and approve visual direction in Figma Make; save relevant screenshots/specifications in the repo when implementation begins.
3. **Implementation:** use Antigravity as the primary code agent, obeying workspace rules and working in small approved slices.
4. **Independent review:** use a different Antigravity model or Claude native when available; request concrete findings and verify them.
5. **Validation:** rely on repository tests, CI, browser tests and reproducible experiments—not model confidence.
6. **Continuity:** update `CURRENT-STATUS.md`, `NEXT-SESSION.md`, ADRs and engineering logs so the next session can resume from the repository.

No model or tool should receive real production credentials, personal secrets or committed environment values. Never paste secrets into project documentation, issue descriptions or review prompts.

## 3. Product and engineering constraints

### Hard constraints

- **Zero-cost by default.** The first complete version must be buildable and demonstrable locally using open-source tools and Docker. No paid cloud hosting, managed database, hosted Redis, managed broker, domain purchase, paid API, paid monitoring or subscription dependency without explicit owner approval.
- **No AWS runtime dependency.** The owner has an AWS Cloud Practitioner certification and may list foundational AWS knowledge accurately, but this project will not claim AWS deployment or hands-on AWS implementation unless one is genuinely completed and verified later. An optional future cloud mapping is documentation only, not an MVP dependency.
- Docker, TypeScript and Next.js are explicit portfolio requirements and remain in the baseline.
- No premature distributed-system sprawl. Begin with a modular monolith and a separately run worker process. Do not create microservices just to make an architecture diagram look sophisticated.
- Human approval is required for meaningful architectural changes, paid tools, new infrastructure, major dependencies and scope expansion.

### Quality constraints

- Security, testability, observability and documentation are built alongside features.
- Every performance claim must be backed by a reproducible test and disclosed environment.
- Prefer understandable, standard engineering over clever abstractions.
- Keep APIs and components small enough to reason about.
- Do not add a dependency solely because an AI model happens to know it.

## 4. Approved baseline stack (M0)

| Layer | Baseline choice | Reasoning |
|---|---|---|
| Web app | Next.js + TypeScript | Strong frontend product surface and a required resume skill |
| API / redirect service | Node.js + Express 5 + TypeScript | Explicit backend boundary, control over redirect path and backend learning |
| Persistence | PostgreSQL | Ownership relations, link lifecycle, event aggregation and transactional integrity fit a relational model |
| Data access | Drizzle ORM | Typed schema/migrations with visible SQL and query behaviour |
| Runtime validation | Zod | Validate untrusted HTTP input, environment configuration and queue payloads |
| Cache | Redis | Hot code-to-destination lookups and shared rate-limit state |
| Message broker | RabbitMQ | Dedicated broker for decoupled analytics; queueing concerns remain separate from cache concerns |
| Async processor | Separate TypeScript worker | Independent consumer lifecycle, retry policy and concurrency controls |
| Local runtime | Docker + Docker Compose | Reproducible app, database, cache and broker on a local machine |
| Tests | Vitest, Supertest, Playwright, k6 | Unit/integration, HTTP/API, end-to-end browser and load tests |
| CI | GitHub Actions | Repeatable checks on pushes and pull requests |
| UI design | Figma Education / Figma Make | Design exploration, visual iteration and handoff before implementation |
| Logging/telemetry | Structured logs first; OpenTelemetry-compatible instrumentation as scope matures | Diagnose requests and worker behaviour without a paid observability platform |

This is an approved **baseline**, not a promise that every dependency is installed today. Select exact versions at M1 based on current supported releases, pin them, and record them in package manifests/lockfiles. Do not invent version numbers in documentation before checking at implementation time.

### Explicitly not in the initial stack

AWS services, Kubernetes, Kafka, ClickHouse, microservices, serverless decomposition, multi-region replication, paid hosted analytics, custom domain procurement, billing, AI summaries, QR-code campaigns and sophisticated fraud scoring. Some may appear in future scope, but none are necessary to prove the core engineering objectives.

## 5. System shape

Initial architecture is a **modular monolith plus an independent worker process**:

```text
                  ┌─────────────────────────────┐
                  │       Browser / User        │
                  └──────────────┬──────────────┘
                                 │ HTTPS / local HTTP
                  ┌──────────────▼──────────────┐
                  │       Next.js + TS          │
                  │  marketing + dashboard UI   │
                  └──────────────┬──────────────┘
                                 │ API calls
                  ┌──────────────▼──────────────┐
                  │     Express API + TS        │
                  │ auth / links / redirect     │
                  └──────┬────────────┬─────────┘
                         │            │
                  ┌──────▼─────┐  ┌───▼───────────┐
                  │ PostgreSQL │  │ Redis         │
                  │ source of  │  │ cache +       │
                  │ truth      │  │ rate limits   │
                  └──────┬─────┘  └───────────────┘
                         │
                  ┌──────▼─────────────┐
                  │ RabbitMQ           │
                  │ click-event queue  │
                  └──────┬─────────────┘
                         │
                  ┌──────▼─────────────┐
                  │ Analytics worker   │
                  │ TS consumer         │
                  └──────┬─────────────┘
                         │
                  ┌──────▼─────────────┐
                  │ PostgreSQL         │
                  │ analytics storage  │
                  └────────────────────┘
```

The diagram is logical, not a final deployment topology. Keep clear module boundaries in a single repository. The worker is a separate process so it can have its own lifecycle and concurrency without becoming a microservice network.

## 6. Important request flows

### 6.1 Create a link

1. Browser sends an authenticated request to the API.
2. API validates request shape and destination protocol.
3. Authenticated identity is obtained from server-side session context; ownership is never taken from a client-supplied `userId`.
4. API generates a short code using a cryptographically secure random source, checks/persists it under a database unique constraint and handles rare collisions with a bounded retry.
5. PostgreSQL stores the link as the durable source of truth.
6. API returns the canonical short URL and link metadata.

Custom alias support is a product requirement candidate, but define reserved words, character set, uniqueness and abuse controls before implementation. Never accept `http`, `login`, `api`, `admin`, health endpoints or other reserved route names as aliases.

### 6.2 Redirect

1. Visitor requests `/{code}` on the redirect service.
2. Validate code syntax and resolve the mapping from Redis.
3. On cache miss, read an active, non-expired link from PostgreSQL; populate Redis with a bounded TTL.
4. Return the redirect using an explicit redirect status policy (decide and document permanent vs temporary semantics before shipping; temporary redirects are safer while links remain editable).
5. Emit a click event to RabbitMQ without making analytics processing part of the critical path.
6. If Redis is unavailable, fall back to PostgreSQL rather than failing every redirect. If analytics publication fails, prioritize a valid redirect, emit structured diagnostics and make potential event loss observable.

Caching details to design and test: TTL policy, inactive/expired link handling, short negative-cache TTL, invalidation after edit/disable/delete, stale-entry race windows and Redis-down fallback. Do not cache disabled links for long periods. Cache invalidation must happen after the database mutation commits; if invalidation fails, document the maximum stale window and how it is monitored.

### 6.3 Analytics processing

1. Redirect service emits a versioned, validated event with a stable event ID.
2. RabbitMQ durable queue and publisher/consumer behaviour are configured deliberately.
3. Worker validates the payload and records/aggregates the event.
4. Worker acknowledges only after durable processing succeeds.
5. Transient failures retry with bounded policy; poison messages go to a dead-letter path for inspection rather than looping forever.
6. Consumer is idempotent because at-least-once delivery may redeliver messages. A unique event ID or equivalent deduplication constraint prevents double counting.

Never claim exactly-once processing or end-to-end at-least-once delivery for every redirect. The analytics reliability contract has distinct stages: **best-effort bounded publication** (redirect proceeds regardless of broker confirmation; publication timeouts are indeterminate rather than definitive drops; pre-broker loss is accepted and observable) and **at-least-once processing of confirmed, routed messages** (idempotent consumer with durable acknowledgement and dead-letter routing for poison messages). A transactional outbox is a documented future enhancement if measured pre-confirmation event loss proves unacceptable; it is not included in the initial MVP. See ADR-006.

## 7. Data and product model direction

Initial conceptual entities:

- **User:** stable ID, normalized email, password hash or external identity reference (authentication approach is an M0 open decision), timestamps, status.
- **Link:** stable ID, owner ID, short code, destination URL, optional title/description, status, created/updated timestamps, optional expiry, optional custom alias metadata.
- **ClickEvent / aggregate:** event ID, link ID, event timestamp, carefully minimized request metadata and processing status as needed.
- **Daily/period aggregate:** optional roll-up table or materialized aggregation model if raw event queries become expensive.

Do not lock physical schema in M0. During M1/M2, define constraints, indexes, deletion/retention behaviour and migration policy with ADRs. Important constraints include unique short code, owner foreign key, explicit link status, valid expiry semantics and idempotent analytics event identity.

### Analytics data minimization

The MVP should avoid storing raw IP addresses. Do not introduce geolocation enrichment that requires a third-party API. Consider collecting only what the product needs: timestamp, link ID, a coarse user-agent-derived device/browser class, and a privacy-conscious referrer value with query-string/fragment stripping where practical. Document retention and aggregation policy before analytics implementation. User-agent/referrer data can still contain sensitive information; collect the minimum and avoid displaying untrusted values as executable content.

## 8. Security and abuse posture

Pulse is a URL shortener, so redirecting users to arbitrary public destinations is core functionality and also an abuse surface. Do not claim that URL syntax validation makes destinations safe. Build operational controls: report/takedown workflow, link disablement, rate limits on creation, clear abuse/error pages and conservative handling of suspicious input.

MVP security principles:

- Accept only `http:` and `https:` destinations; reject `javascript:`, `data:`, `file:`, protocol-relative and malformed schemes.
- Do not fetch destination URLs from the server in MVP. No preview scraping, metadata fetch, webhook or URL expansion feature. This deliberately avoids an SSRF-capable server fetch path. Any future fetching feature requires a separate threat model and ADR, including DNS rebinding and private/link-local address controls.
- Use secure password handling if password auth is selected; never store plaintext credentials.
- Use secure, HttpOnly, appropriately SameSite cookies for session material and CSRF protection consistent with the chosen auth model.
- Enforce server-side ownership checks for every private resource operation (BOLA/IDOR prevention).
- Use parameterized SQL; keep DB credentials least-privileged and local by default.
- Validate HTTP requests, query parameters, environment variables and queue events at runtime.
- Add rate limits to authentication and link-creation endpoints. Redirect traffic needs separate policy so popularity of a legitimate link is not accidentally throttled.
- Add security headers, sensible CORS policy, safe error responses and structured security-relevant audit events without leaking secrets.
- Escape user-controlled link titles, aliases and referrers in the UI; no unsafe raw HTML rendering.
- Protect admin/abuse actions with clear authorization and auditability.

The detailed security checklist is the source of truth for verification status. At M0 all implementation controls are pending; documentation is not proof that they exist.

## 9. Performance and reliability philosophy

The most important performance path is short-code resolution and redirect. Avoid synchronous analytics aggregation, third-party calls and unnecessary database joins in that path. Cache the hot code-to-link mapping, index the lookup key, measure query plans and use connection pooling deliberately.

Performance work must be evidence-led:

- Establish a reproducible baseline before tuning.
- Use k6 scenarios for warm cache, cold cache, mixed read/write, and failure/degraded dependency conditions.
- Report request rate, p50/p95/p99 latency, error rate, CPU/memory, machine/runtime/container details, test duration and virtual-user model.
- Separate local synthetic benchmark numbers from real production traffic.
- Identify bottlenecks before adding infrastructure.

Resilience experiments should include Redis unavailable, RabbitMQ unavailable, worker crash/restart, duplicate event delivery, poison event, PostgreSQL slowdown/unavailability, expired/disabled link, cache invalidation failure and high request volume. Define expected behaviour before running each test.

Do not promise a specific RPS target at M0. Establish and calibrate acceptance thresholds on the actual development environment at M7; publish only results that were actually run.

## 10. Collaboration model: human owner + AI agents

The owner makes final product and architecture decisions. Agents are expected to be proactive technical collaborators, not passive autocomplete and not autonomous architects.

### Required workflow

1. **Discovery:** inspect the repo and relevant documentation; resolve ambiguity without making hidden assumptions.
2. **Technical plan:** give a concise description of the problem, acceptance criteria, files, proposed design, alternatives, dependencies, risks and test strategy.
3. **Approval:** pause for owner approval when the task changes architecture, security posture, data model, significant dependencies, paid services or scope.
4. **Implementation:** make a focused change; no giant code dump or unrelated refactor.
5. **Verification:** run relevant tests, typecheck, lint, build, migration checks and security review available in the repository. Report exact commands and actual output summary.
6. **Independent review:** when available, ask a different model/agent to inspect the diff for correctness, edge cases and security. Verify review comments rather than accepting them blindly.
7. **Documentation:** update ADRs, security checklist, engineering log, current status and next-session handoff when knowledge changes.
8. **Teaching:** explain the code path, design rationale, alternatives, limitations, failure modes and likely interview questions.

### AI must not

- silently change an approved architecture decision;
- invent files, scripts, APIs, package versions or passing test results;
- suppress a compiler/linter/test warning just to get green output;
- disable authentication, validation, rate limiting or security headers as a quick fix;
- add unnecessary abstractions or dependencies;
- claim production readiness or scalability without evidence;
- store secrets in code, logs, prompts or committed files;
- make broad refactors without approval.

### Debugging standard

For a non-trivial bug, provide: observed symptom, exact reproduction, expected vs actual behaviour, relevant logs/stack traces, root-cause hypothesis, evidence that confirms it, minimal fix, regression test and remaining risk. If the cause is unknown, say so and propose the next diagnostic step rather than guessing.

## 11. Documentation system

Keep documents useful and non-duplicative:

- PRD: product outcomes, users, requirements and acceptance criteria.
- Architecture: current system design and key flows (update when architecture evolves).
- ADRs: why a durable choice was selected and when to revisit it.
- Security checklist: threats, control, owner/location, verification evidence and status.
- Milestone docs: scope, tasks, exit criteria and retrospective.
- Engineering log: significant bugs, performance experiments and failure experiments.
- Memory: concise state and next steps for continuity.

When a decision changes, update its ADR status and relevant architecture/docs together. Keep `CURRENT-STATUS.md` and `NEXT-SESSION.md` short and accurate. Never let the handoff imply code exists when only a plan exists.

## 12. Current state and immediate next action

- Current milestone: M0.
- Current implementation state: documentation-only foundation; no source code or runtime implementation yet.
- Stack direction: agreed baseline in this handoff and ADRs, subject to owner review of the remaining open questions.
- No GitHub repository is linked/accessible to the current assistant integration at the time this handoff was prepared. Repository publication remains an external setup step; do not claim a push occurred.

**First working session:** review M0 open questions and baseline decisions with the owner; close or explicitly defer each one; then propose the M1 repository/runtime foundation plan. Do not jump directly into feature endpoints.

## 13. Useful Antigravity customization references

Antigravity supports project-scoped persistent instructions in `AGENTS.md` / `GEMINI.md` and modular rule files in `.agents/rules/`. The repository includes both a compact root `AGENTS.md` and a workflow rule under `.agents/rules/`. Official reference: <https://antigravity.google/docs/rules/>. Always check current product behaviour if customization mechanics change.
