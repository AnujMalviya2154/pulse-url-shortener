# M0 — Foundation milestone

**Objective:** Establish a shared product, architecture, security and collaboration baseline before writing application code.

M0 is intentionally documentation-led. It prevents the project from beginning as disconnected AI-generated code and gives Antigravity a persistent source of truth.

## M0 deliverables

- [x] Initial product requirements drafted (`docs/PRD.md`).
- [x] Initial architecture documented (`docs/ARCHITECTURE.md`).
- [x] Initial security threat register drafted (`docs/SECURITY-CHECKLIST.md`).
- [x] Milestone roadmap drafted (`docs/ROADMAP.md`).
- [x] Antigravity project brief drafted (`docs/ANTIGRAVITY-HANDOFF.md`).
- [x] Workspace guardrails provided (`AGENTS.md`, `.agents/rules/implementation-workflow.md`).
- [x] First ADR set created (`docs/decisions/`).
- [x] Open questions and M1 first-session plan recorded.
- [ ] Owner has reviewed and approved the baseline.
- [ ] Every open question is resolved or explicitly deferred with rationale.
- [ ] M1 plan is approved.

The checkboxes above describe document preparation status at handoff time; the final three owner-review steps are deliberately left open.

## M0 exit review

At the M0 review, the owner and agent should confirm:

1. Is the product scope compelling but still small enough to finish?
2. Is the baseline stack still aligned with learning and zero-cost constraints?
3. Is RabbitMQ worth the separate broker vs the simpler BullMQ + Redis alternative?
4. Which authentication/session model is appropriate for a local-first MVP?
5. What is the minimum analytics dataset that creates useful value without collecting unnecessary personal data?
6. What is the minimum abuse-report/takedown workflow before any public exposure?
7. What evidence must be present before a milestone can be called complete?

## M0 completion record

Do not mark M0 complete until the owner has reviewed the ADRs/open questions and approved the M1 implementation plan. Record the date, approval notes, deferred decisions and next milestone commit in `docs/memory/CURRENT-STATUS.md`.
