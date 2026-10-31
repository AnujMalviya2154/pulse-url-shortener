---
trigger: always_on
description: Pulse-specific engineering workflow, quality gates, and learning expectations.
---

# Pulse implementation workflow

- Treat `docs/ANTIGRAVITY-HANDOFF.md` as the detailed collaboration contract and `AGENTS.md` as the always-on invariant list.
- Before code changes, report task understanding, acceptance criteria, files affected, plan, dependencies, risks, and tests. Obtain the owner's approval for significant choices.
- Inspect the repository before editing. Never assume files, scripts, package versions, schema state or deployed behaviour.
- Make focused, reversible changes. Avoid drive-by refactors and unrequested scope.
- Use strict TypeScript and runtime validation. Do not hide type errors with `any`, broad casts or ignored diagnostics.
- Never hard-code credentials. Keep `.env.example` limited to placeholder names and safe local examples. Never commit `.env` or secrets.
- API endpoints require schema validation, authentication/authorization where applicable, consistent errors and appropriate rate limits.
- A user must only read or modify links and analytics they own. Ownership comes from authenticated server-side context, never a request body field.
- Redirect destinations accept only HTTP and HTTPS. The MVP does not fetch destination URLs from the server; do not add previews, metadata scraping or webhook fetching without an SSRF threat review and an approved ADR.
- Keep analytics asynchronous and isolated from redirect availability. Make delivery guarantees explicit; design consumers to tolerate duplicate delivery.
- Run relevant tests and report exact outcomes. A test not run is not a passing test.
- Update the relevant docs and project memory whenever an approved decision, interface, security property, bug root cause or milestone state changes.
- Explain important engineering concepts and interview-relevant trade-offs as implementation proceeds.
