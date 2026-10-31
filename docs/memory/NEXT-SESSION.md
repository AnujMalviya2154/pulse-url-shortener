# Next session — start here

**Target start:** 1 November 2026
**Starting milestone:** M0 review, then M1 planning (not feature coding yet)

## First 15–30 minutes

1. Open the Pulse repository folder in Antigravity as the workspace root.
2. Confirm the agent has loaded root `AGENTS.md` and `.agents/rules/implementation-workflow.md` (ask it to summarise the project guardrails and report which files it read).
3. Ask the agent to read `docs/ANTIGRAVITY-HANDOFF.md`, `docs/PRD.md`, `docs/ARCHITECTURE.md`, `docs/SECURITY-CHECKLIST.md`, and the ADRs.
4. Review `docs/milestones/M0/M0-OPEN-QUESTIONS.md`; resolve or explicitly defer each decision.
5. Update `docs/memory/CURRENT-STATUS.md` with owner decisions and date.
6. Approve M0 sign-off only after the review. Then ask for a small M1 implementation plan.

## Prompt to paste into Antigravity

```text
We are resuming the Pulse project. Do not start coding yet.

First, inspect the repository and read AGENTS.md, .agents/rules/implementation-workflow.md, docs/ANTIGRAVITY-HANDOFF.md, docs/PRD.md, docs/ARCHITECTURE.md, docs/SECURITY-CHECKLIST.md, docs/ROADMAP.md, all current ADRs, docs/milestones/M0/M0-OPEN-QUESTIONS.md, and docs/memory/CURRENT-STATUS.md.

Then report:
1. Your concise understanding of Pulse, its purpose, constraints and approved baseline stack.
2. The rules you will follow while working with me.
3. The current milestone and what is/is not implemented.
4. Every unresolved M0 decision, with trade-offs and a recommendation, without silently deciding.
5. Any contradictions, missing requirements, security concerns or documentation gaps you found.

Do not change files or create application code during this review. After I approve the M0 decisions, propose a focused M1 repository/runtime foundation plan with scope, file changes, dependencies, acceptance criteria, risks, tests and documentation updates. Wait for my approval before implementing.

Remember: I am learning this stack. Explain the important engineering concepts and interview-relevant trade-offs as we work. Never claim a test, build, scan, benchmark or deployment succeeded unless you actually ran and verified it.
```

## M1 planning guardrail

M1 is infrastructure and developer-experience foundation only. Do not start with link CRUD or redirect features. First make the repository, TypeScript setup, Docker Compose dependencies, environment validation, health checks, migrations, test harness and CI repeatable.
