# Pulse — compact project memory

## Product

Pulse is a polished URL shortener and link analytics service. Users create/manage short links, visitors redirect through public short-code resolution, and click analytics are processed asynchronously.

## Owner goals

Build an interview-grade full-stack/backend project demonstrating TypeScript, Next.js, Docker, APIs, PostgreSQL, Redis, queues, security, reliability, observability and measured performance. Owner is learning the stack and expects agents to teach concepts, proactively catch issues and document trade-offs. Owner approves meaningful architecture changes.

## Cost and cloud

Zero-cost/local-first hard constraint. No AWS runtime dependency, paid hosting, managed data stores, paid APIs or domains without explicit approval. Do not claim AWS hands-on deployment; AWS Cloud Practitioner is foundational certification/knowledge only unless actual deployment is later completed.

## Approved baseline

Next.js + TypeScript; Node.js + Express 5 + TypeScript; PostgreSQL; Drizzle; Zod; Redis for cache and rate-limit state; RabbitMQ for asynchronous analytics; separate TypeScript worker; Docker Compose; Vitest; Supertest; Playwright; k6; GitHub Actions; Figma Education/Figma Make for design.

## Architecture stance

Modular monolith plus separate worker. PostgreSQL is source of truth. Redis is disposable cache. RabbitMQ is analytics event broker. Redirect availability takes priority over analytics aggregation; use at-least-once delivery plus idempotent consumer; no exactly-once claims. Do not fetch destination URLs server-side in MVP.

## Development workflow

Understand → plan → approve → implement → test → independent review → document → handoff. Small scoped changes. Strict TypeScript. Zod at trust boundaries. No secrets in code. No fabricated test results. Regression tests for bugs. ADRs for significant decisions. Engineering log for non-trivial bugs/experiments. Keep current status and next-session notes updated.

## Tool roles

- Antigravity IDE (enhanced with MCP + Skills) is the primary coding environment. Use model choice based on task and quota; run independent review with another model when feasible.
- ChatGPT is the architecture/continuity/tutoring coordinator; checked-in docs remain canonical memory.
- Figma Education / Figma Make is used for UI/UX exploration and approved design handoff.
- Claude native models and the owner-reported Claude skills (UI/UX Pro Max, Taste, WebTest, Impeccable, Memory, Find) are optional review/design/testing aids; do not assume they are installed in Antigravity.
- OmniRoute is optional only; owner currently has no Claude Code access, so Pulse must not depend on Claude Code/Cowork/OmniRoute.

## Current state

M0 docs are drafted. No app code exists. Next: review open questions, sign off M0, then plan M1. See `CURRENT-STATUS.md` and `NEXT-SESSION.md`.
