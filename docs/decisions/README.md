# Architecture Decision Records

An ADR records a durable technical decision. Keep each record short enough to review and specific enough to explain why a choice was made.

## Status vocabulary

- **Proposed:** written but not approved.
- **Accepted:** approved and currently in force.
- **Superseded:** replaced by a later ADR (link the replacement).
- **Deprecated:** no longer recommended, retained for historical context.

## Template

```markdown
# ADR-NNN: Title
- Status:
- Date:

## Context
What problem or constraint requires a decision?

## Decision
What are we choosing?

## Alternatives considered
What credible alternatives were evaluated and why were they not selected?

## Consequences
Trade-offs, operational implications and new risks.

## Revisit when
What evidence or changed requirement would justify reconsidering it?
```

## Current records

| ADR | Topic | Status |
|---|---|---|
| [ADR-001](ADR-001-local-first-no-cloud-runtime.md) | Local-first / no cloud runtime | Accepted baseline |
| [ADR-002](ADR-002-modular-monolith-and-separate-worker.md) | Modular monolith + worker | Accepted baseline |
| [ADR-003](ADR-003-postgresql-as-source-of-truth.md) | PostgreSQL source of truth | Accepted baseline |
| [ADR-004](ADR-004-redis-for-cache-rabbitmq-for-analytics.md) | Redis cache + RabbitMQ broker | Accepted (approved 1 Nov 2026) |
| [ADR-005](ADR-005-cryptographically-random-short-codes.md) | Random short codes | Proposed; finalize before M3 |
| [ADR-006](ADR-006-at-least-once-analytics-processing.md) | Analytics reliability contract | Accepted (approved 1 Nov 2026) |
