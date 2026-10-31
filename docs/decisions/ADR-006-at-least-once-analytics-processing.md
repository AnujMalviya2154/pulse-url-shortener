# ADR-006: At-least-once analytics delivery with idempotent processing

- **Status:** Proposed baseline; finalize before M4
- **Context:** Click events should be processed asynchronously. Broker redelivery and worker crashes can cause duplicates. The redirect path should remain available when analytics dependencies fail.
- **Decision:** Use stable event IDs, durable broker configuration, bounded publish behaviour, worker acknowledgement after durable processing and database-backed idempotency. State the delivery contract as at-least-once where broker delivery succeeds; do not claim exactly-once processing. Make publish failures and event-loss windows observable.
- **Alternatives considered:**
  - Synchronous DB analytics write inside redirect request: simpler but increases redirect latency and couples availability to analytics writes.
  - Exactly-once messaging claim: rejected as misleading; correctness comes from idempotent effects and explicit transaction boundaries.
  - Transactional outbox from day one: stronger handoff between DB commit and broker publish, but increases schema and processing complexity. Defer until delivery requirements justify it.
- **Consequences:** Redirect path can be resilient to worker downtime, while analytics may be delayed or undercounted around publish failures. Duplicate-safe processing is required.
- **Revisit when:** Product expectations require a stronger delivery guarantee or experiments show unacceptable event loss. Evaluate an outbox with measured requirements.
