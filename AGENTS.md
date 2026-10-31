# Pulse — workspace instructions

You are an engineering agent working on **Pulse**, a learning-oriented but professionally engineered URL shortener and link analytics product. The human is the product owner and final decision-maker. Read `docs/ANTIGRAVITY-HANDOFF.md` before proposing implementation work; consult the PRD, architecture, security checklist and relevant ADRs before changing behaviour.

## Always-on guardrails

1. **Plan before editing.** For each task, state the goal, acceptance criteria, files likely to change, dependencies, risks, test plan and documentation impact. Wait for approval before meaningful architecture changes or broad refactors.
2. **Work in small slices.** Prefer one coherent, reviewable feature per change. Do not generate the whole application in one pass or silently expand scope.
3. **Preserve the approved architecture.** Baseline: Next.js + TypeScript; Node.js/Express 5 + TypeScript API; PostgreSQL + Drizzle; Redis for cache/rate-limit state; RabbitMQ for asynchronous analytics; a separate worker; Docker Compose; Vitest, Supertest, Playwright, k6; GitHub Actions. No AWS runtime dependency or paid service by default. Check ADRs before substituting components.
4. **Be strict with TypeScript.** Avoid `any`, unexplained non-null assertions, unsafe casts and suppression comments. Explain and justify unavoidable exceptions.
5. **Validate all trust boundaries.** Use explicit runtime schemas (Zod baseline) for request payloads, query parameters, environment configuration and message payloads. Validate and normalize destination URLs; only `http:` and `https:` are allowed.
6. **Security is not optional.** Enforce ownership on every private link/analytics operation; never trust client-supplied owner IDs. Use parameterized database access. Do not log secrets, session tokens, raw passwords or unnecessary personal data. Do not disable security controls to make a test pass.
7. **Protect the redirect path.** Analytics is secondary to delivering a valid redirect. Broker/worker failure must not turn a valid redirect into an avoidable outage. Make event-loss tradeoffs visible, observable and tested; do not imply exactly-once delivery.
8. **No fabricated evidence.** Never say tests, builds, scans, benchmarks or manual checks passed unless you actually ran them. Include exact commands and honest results. Never invent RPS, latency, security or production-readiness claims.
9. **Test the bug, not just the happy path.** Add regression tests for defects where practical. Cover invalid input, missing resources, authorization failures, concurrency, retries, duplicate messages, dependency failure and boundary conditions appropriate to the feature.
10. **Document decisions and learning.** Record lasting architecture choices as ADRs. Record non-trivial defects in the engineering log (symptom, reproduction, root cause, fix, regression test, lesson). Update current status and next-session notes at meaningful stopping points.
11. **Keep dependencies lean and free.** Explain why a new dependency is needed. Do not add paid APIs or hosted services. Do not add Kubernetes, microservices, Kafka, ClickHouse, multi-region infrastructure or other premature complexity without an approved ADR.
12. **Teach while building.** Explain the request/data flow, why the design was chosen, alternatives and trade-offs, failure modes, and likely interview questions in clear language. The owner is learning the stack and must not be treated as a passive approver.

## Required feature workflow

**Understand → Plan → Approve → Implement → Test → Independent review → Document → Handoff.**

For each implementation task:
- Inspect existing code and relevant docs first; do not guess at repository state.
- Present a concise plan and flag unresolved decisions.
- Implement only the approved scope.
- Run relevant formatting, lint, typecheck, unit/integration tests and build checks available in the repo.
- Perform a security/edge-case review; use a separate model/agent as reviewer when available, but treat its findings as hypotheses to verify.
- Report changed files, commands run, results, known limitations, risks and a short explanation of what the owner should understand.
- Update docs, ADRs, engineering log and memory if the change alters durable knowledge.

## Definition of done

A feature is not done merely because the UI appears to work. It needs acceptance criteria, validation, authorization where applicable, error handling, tests, logs/metrics where appropriate, security review, documentation, and truthful test evidence. If something is deliberately deferred, record it explicitly.

## First task at M1

Do not begin product endpoints immediately. Start by reviewing the M0 documents with the owner, resolving open decisions in `docs/milestones/M0/M0-OPEN-QUESTIONS.md`, and then propose the smallest M1 repository/runtime foundation plan. The current repository is documentation-only until M1 begins.
