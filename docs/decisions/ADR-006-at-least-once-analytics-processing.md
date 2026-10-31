# ADR-006: Analytics reliability contract — best-effort bounded publication, indeterminate timeouts, at-least-once consumer processing

- **Status:** Accepted — approved at M0 review, 1 November 2026
- **Context:** Click events must be processed asynchronously so analytics collection does not degrade redirect latency or compromise availability. The Express API publishes events to RabbitMQ; a separate background worker consumes and aggregates them into PostgreSQL. Network partitions, broker downtime, unroutable bindings, worker crashes, and poison messages can occur. The system requires an honest, interview-defensible distributed reliability model.
- **Decision:** The analytics reliability pipeline is governed by a four-stage contract:
  1. **Publication phase (redirect handler → RabbitMQ):** Best-effort with bounded wait. The redirect handler initiates publication with a bounded wait budget (50ms is an initial illustrative candidate budget to be measured and calibrated during M4 performance testing).
     - **Deterministic Failures:** If the broker connection is actively refused, reset, or unavailable, publication fails deterministically and the event is dropped pre-broker. The redirect proceeds immediately.
     - **Indeterminate / Unconfirmed Timeouts:** If the wait budget expires before RabbitMQ acknowledges the message via a publisher confirm, the outcome is **indeterminate**. The message may already be buffered in the OS socket or accepted by the broker, and could still be delivered and processed later. The redirect path stops waiting and issues the HTTP redirect at the deadline. The timeout represents an *unconfirmed* state, not proof of event loss.
  2. **Broker ingestion & routing phase (M4 implementation):**
     - Persistent delivery mode (`deliveryMode: 2`) on durable exchanges and queues.
     - Publisher confirms enabled on the channel to track broker persistence.
     - `mandatory: true` flag enabled on publication, with active listeners for `basic.return` events. A publisher confirm proves the exchange received the message; mandatory return handling deterministically catches unroutable messages (e.g. missing bindings) that would otherwise be silently dropped.
  3. **Processing phase (worker consumer):** At-least-once processing for confirmed, routed messages. The consumer validates the message schema, enforces idempotent deduplication in PostgreSQL via stable event IDs (`INSERT ... ON CONFLICT DO NOTHING`), and acknowledges (`basic.ack`) only after successful durable storage. Idempotency guarantees protection against:
     - Broker at-least-once message redeliveries (`redelivered: true`).
     - Indeterminate publication timeouts where the message was accepted by the broker and delivered late.
     - Caller-side republications of the same stable `eventId`.
  4. **Failure & dead-letter phase (M4 implementation):**
     - Immediate requeue: `basic.nack(msg, false, true)` or `basic.reject(msg, true)` places the message back in its original queue position where possible, otherwise closer to the queue head. It does *not* implement delayed exponential backoff by itself.
     - Delayed retries: The concrete retry mechanism—choosing between native Quorum delayed retries (or delayed exchange plugins) vs. dead-letter exchanges with per-message TTL retry queues—is deferred to M4 implementation review.
     - DLX transfer guarantees: Unrecoverable messages or those exceeding max retry bounds are rejected without requeue (`basic.reject(msg, false)`). Dead Letter Exchange (DLX) transfer guarantees depend on queue type (Classic vs. Quorum) and dead-letter strategy configuration (e.g. `dead-letter-strategy: at-least-once` on Quorum queues, which requires the `stream_queue` feature flag prerequisite). We do not claim reliable DLQ transfer until the selected topology has been configured, benchmarked, and verified in M4. Messages in the DLQ are terminal and excluded from product analytics unless reprocessed.

  **Explicit Non-Claims:**
  - We do NOT claim end-to-end at-least-once delivery for every redirect. Pre-confirmation event loss and unconfirmed timeout windows are accepted, observable trade-offs.
  - We do NOT claim every confirmed message is processed into analytics if it triggers a terminal failure and lands in the DLQ.
  - No transactional outbox will be implemented in the initial MVP; it is documented as an architectural extension if pre-confirmation event loss exceeds operational thresholds.
- **Alternatives considered:**
  - Synchronous database insert in redirect path: Rejected because it directly couples redirect latency and database availability to analytics load.
  - Fire-and-forget without publisher confirms: Rejected because broker packet drops would be completely unobservable.
  - Transactional outbox pattern from day one: Rejected as premature complexity for the MVP. Writing events to an outbox table in PostgreSQL adds database write overhead to every redirect, defeating the latency benefits.
- **Consequences:** Redirect latency remains minimal and isolated from broker health. Unconfirmed timeouts and publish failures are logged as structured diagnostics. Late deliveries and duplicate sends are safely deduplicated by the consumer.
- **Revisit when:** Production telemetry or k6 reliability benchmarks demonstrate that pre-confirmation event loss or timeout rates are unacceptable for business requirements, justifying the database write overhead of a transactional outbox.
