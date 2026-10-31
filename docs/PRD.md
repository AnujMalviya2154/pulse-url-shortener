# Pulse — Product Requirements Document (PRD)

**Version:** 0.1 (M0 baseline)
**Status:** Draft for owner review
**Date:** 31 October 2026
**Product name:** Pulse (working name; public naming/trademark/domain check deferred)

## 1. Product summary

Pulse is a polished URL-shortening and link-analytics application. It lets users create, manage and share short links, then understand how those links are being used. The product is also an engineering showcase: the implementation should make backend performance, asynchronous processing, security, reliability and system-design trade-offs demonstrable through code, tests, documentation and measured experiments.

## 2. Problem statement

Long URLs are difficult to share, manage and track. A simple shortener solves only the first problem; a useful product also needs ownership, link lifecycle management, reliable redirection and understandable analytics. Pulse aims to provide this focused workflow without becoming a large marketing automation suite.

## 3. Goals

### Product goals

- Let an authenticated user create a short link quickly.
- Resolve a short code reliably and redirect visitors to the intended destination.
- Give users control over their own links: view, search, edit, disable/enable and delete.
- Show useful click analytics without placing heavy analytics processing on the redirect path.
- Provide a clean, responsive interface with thoughtful empty, loading, success and error states.
- Include basic abuse reporting/disablement capability and clear user-facing failure states.

### Engineering / portfolio goals

- Demonstrate Next.js and TypeScript across a real product surface.
- Demonstrate explicit backend/API engineering using Express and TypeScript.
- Demonstrate relational modelling and query/index choices with PostgreSQL.
- Demonstrate cache-aside resolution, TTLs, invalidation and graceful cache degradation with Redis.
- Demonstrate asynchronous processing, acknowledgements, retry/dead-letter handling and idempotency with RabbitMQ.
- Demonstrate containerized local infrastructure with Docker Compose.
- Demonstrate security, automated testing, observability and reproducible load testing.
- Produce architecture decisions and engineering notes that the owner can explain in interviews.

## 4. Non-goals for MVP

- Billing, subscriptions, teams, workspaces or enterprise administration.
- Multi-region deployment, Kubernetes, service mesh or microservices.
- Kafka, ClickHouse or a large data warehouse.
- AI-generated analytics, audience prediction or sophisticated fraud scoring.
- QR-code campaign management, A/B destination routing or marketing automation.
- Server-side destination previews, scraping, link expansion or arbitrary webhook fetching.
- Native mobile application.
- AWS deployment or any paid cloud requirement.
- Guaranteed malicious-site detection. Pulse is a redirect platform, not a malware scanner.

## 5. Target users and primary jobs

### Persona A — Individual creator / professional

Needs short links for profiles, portfolios, posts or presentations and wants to know whether people click them.

**Job:** “Create a short link I can share, then see whether it is getting engagement.”

### Persona B — Small project owner

Needs to manage several links, update destinations without changing the shared short URL, disable old links and compare basic performance.

**Job:** “Keep my shared links organised and retain control over them after publication.”

### Persona C — Visitor

Does not need an account. Wants the short URL to resolve quickly and predictably, or to receive a clear message if the link is invalid, expired or disabled.

**Job:** “Open the short link and reach the intended destination without unnecessary delay.”

## 6. Core user journeys

### Journey 1 — Create and share

1. User signs in.
2. User enters a destination URL and optionally a title/custom alias/expiry.
3. UI validates basic input and API validates it authoritatively.
4. System creates a unique short code and returns a shareable URL.
5. UI confirms creation and offers copy/open actions.

### Journey 2 — Manage a link

1. User opens their dashboard.
2. System lists only links owned by that user.
3. User searches or filters their links.
4. User opens a link detail page and edits the destination or metadata, disables/enables or deletes the link.
5. Redirect resolution reflects the lifecycle change within the documented cache consistency window.

### Journey 3 — Visitor redirect

1. Visitor opens `/{shortCode}`.
2. Service resolves the code using cache then database fallback.
3. Active, non-expired link redirects to its destination.
4. Invalid, expired or disabled code receives a clear, safe response page/status.
5. Click event is queued for asynchronous processing; a broker/worker outage should not unnecessarily prevent a valid redirect.

### Journey 4 — View analytics

1. Owner opens analytics for a link or account.
2. API returns analytics only for links owned by that account.
3. UI shows total clicks, a time-series view and supported breakdowns.
4. UI communicates empty/partial data states honestly; analytics may be eventually consistent.

### Journey 5 — Report abuse (MVP decision to resolve)

A visitor can report a short link, or an operator can disable it through a restricted administrative process. The exact UX, report storage and admin authorization model must be decided before the public demo is exposed to untrusted users.

## 7. Functional requirements

Priority labels: **P0** required for MVP; **P1** desirable after core; **P2** future.

| ID | Requirement | Priority | Acceptance intent |
|---|---|---:|---|
| FR-01 | User registration and sign-in | P0 | Invalid credentials do not reveal sensitive account details; sessions are handled securely |
| FR-02 | User sign-out/session expiry | P0 | Session can be invalidated and expires according to documented policy |
| FR-03 | Create a short link | P0 | Only valid HTTP(S) destinations are accepted; code uniqueness is enforced by DB |
| FR-04 | View own links | P0 | User sees only resources they own; pagination/search behaviour is defined |
| FR-05 | Edit destination/metadata | P0 | Owner-only update; cache invalidation behaviour is tested |
| FR-06 | Disable and re-enable a link | P0 | Disabled links do not redirect after the documented consistency window |
| FR-07 | Delete a link | P0 | Owner-only deletion; deletion semantics and analytics retention are documented |
| FR-08 | Public redirect by short code | P0 | Active, non-expired code redirects to stored destination |
| FR-09 | Invalid/expired/disabled response | P0 | Safe, clear response; no stack traces or internal details leak |
| FR-10 | Asynchronous click-event publication | P0 | Event includes stable ID and version; publication has bounded wait — redirect proceeds at deadline regardless of broker confirmation; timeouts are indeterminate; confirmed messages processed at-least-once with idempotent consumer |
| FR-11 | Idempotent event processing | P0 | Duplicate event delivery does not double-count |
| FR-12 | Basic analytics view | P0 | Owner-only metrics; totals and a time series are available |
| FR-13 | Custom alias | P1 | Reserved words, character rules and collisions are handled safely |
| FR-14 | Link expiry | P1 | Expired links stop redirecting according to defined semantics |
| FR-15 | Abuse reporting / operator takedown | P0 before public exposure; P1 for local-only demo | A report or operator action can disable a harmful link with audit trail |
| FR-16 | Export analytics | P2 | Data export has access control and privacy safeguards |

## 8. Non-functional requirements

### Performance

- Redirect resolution is the primary latency-sensitive path.
- Use Redis for hot mapping lookups, with PostgreSQL as source of truth.
- Analytics computation is asynchronous and does not execute synchronously in redirect handling.
- Measure p50/p95/p99 latency, throughput, error rate, and resource use with reproducible k6 scenarios.
- Numerical performance thresholds will be calibrated from a baseline on the actual local environment; no unverified RPS claim is approved at M0.

### Reliability

- Redis failure must have a documented PostgreSQL fallback.
- RabbitMQ or worker failure must not prevent valid redirects. Analytics publication has a bounded wait budget; timeouts represent indeterminate publication outcomes rather than confirmed drops, and late deliveries are safely deduplicated. Post-confirmation processing uses at-least-once semantics with idempotent consumers and dead-letter queue routing for poison messages. Event loss and degradation must be logged and made observable.
- Worker processing must handle redelivery, duplicate messages and poison messages.
- Health/readiness endpoints must distinguish process health from dependency readiness where appropriate.

### Security and privacy

- Apply authentication and owner-scoped authorization to private routes.
- Validate all untrusted HTTP and message input.
- Accept only HTTP(S) destination URLs.
- Do not fetch destination URLs from the server in MVP.
- Minimize analytics data and do not persist raw IP addresses in the initial design.
- Use secure session handling, rate limits, safe logging, security headers and least-privilege credentials.
- Maintain a threat-driven checklist and evidence for each control.

### Usability and accessibility

- Responsive layout for desktop and mobile widths.
- Keyboard-operable core flows and visible focus states.
- Label form fields and communicate validation errors accessibly.
- Use consistent loading, empty, success and error states.
- Avoid chart-only communication; show key values in readable text.

### Maintainability

- Strict TypeScript and clear module boundaries.
- Runtime validation for external input.
- Versioned migrations and repeatable local setup.
- Automated checks in CI.
- ADRs for architecture choices and concise handoff notes.

### Cost

- Local-first with Docker Compose and open-source components.
- No paid infrastructure or external API required to run the MVP.
- Any optional hosted demo must be reviewed separately for current free-tier limits, data handling and potential charges before use.

## 9. Success measures

Product-level measures:

- A new user can create and copy a link without assistance.
- A visitor can resolve a valid code and receives a clear response for invalid/disabled/expired codes.
- Owner analytics reflect processed events and communicate their eventual consistency.

Engineering measures:

- Clean local bootstrap from documented instructions.
- Typecheck, lint, automated tests and production build run successfully in CI.
- Ownership and redirect validation tests cover negative cases.
- Redis and RabbitMQ failure scenarios are exercised and documented.
- k6 report records environment, test model, latency percentiles, error rate and throughput.
- The owner can explain each major component, a key alternative, and at least one failure mode/trade-off.

## 10. Product policies and open decisions

The following are intentionally not silently decided in the PRD:

1. Authentication model and session implementation (password/session strategy, cookie policy, password hashing choice).
2. Redirect HTTP status (temporary vs permanent) and cache/browser semantics.
3. ~~Custom aliases and expiry inclusion in MVP or early P1.~~ **Decided 1 Nov 2026:** deferred to P1. Core MVP ships without them.
4. Analytics retention period and deletion interaction.
5. Exact metadata fields stored per click and aggregation granularity.
6. Abuse-report UX, operator role and takedown audit model.
7. ~~Whether publisher-confirmed delivery is sufficient for the first release or whether a transactional outbox is justified.~~ **Decided 1 Nov 2026:** best-effort bounded publication accepted for MVP with indeterminate timeout semantics. Transactional outbox documented as a future enhancement if measurements show unacceptable event loss. See ADR-006.
8. Exact performance SLO after baseline testing.

Track these in [`milestones/M0/M0-OPEN-QUESTIONS.md`](milestones/M0/M0-OPEN-QUESTIONS.md) and resolve or defer explicitly.

## 11. MVP release acceptance criteria

MVP is ready for a portfolio demonstration only when:

- A user can register/sign in and manage only their own links.
- Link create/edit/disable/delete flows are validated and tested.
- Public redirect works for active links and returns safe responses for invalid/expired/disabled codes.
- Destination validation rejects unsupported schemes and malformed input.
- Redis caching, PostgreSQL fallback and cache invalidation are tested.
- Analytics events are processed asynchronously and idempotently, with retry/dead-letter behaviour documented.
- Analytics UI is owner-scoped and communicates eventual consistency.
- Security checklist P0 controls are implemented and have test evidence.
- Docker Compose brings up the local stack with clear setup instructions.
- CI checks and relevant tests pass; exact commands are documented.
- k6 performance tests are reproducible and published with honest measurements.
- README, architecture, ADRs, security checklist and engineering log match the implementation.

## 12. M0 scope and exit criteria

M0 is complete when product scope, baseline architecture, zero-cost boundary, threat model, collaboration rules, ADRs, initial roadmap and M1 open decisions are reviewed. M0 does not include application endpoints or deployed infrastructure. See [`milestones/M0/M0-CHECKLIST.md`](milestones/M0/M0-CHECKLIST.md).
