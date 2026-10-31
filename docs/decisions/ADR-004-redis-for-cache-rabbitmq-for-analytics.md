# ADR-004: Redis for cache; RabbitMQ for asynchronous analytics

- **Status:** Accepted — approved at M0 review, 1 November 2026
- **Context:** Redirect resolution benefits from a hot key-value cache. Click analytics requires durable asynchronous messaging, retries and consumer acknowledgement. Combining both responsibilities in one Redis deployment would reduce services but couple cache eviction/memory pressure to queue semantics.
- **Decision:** Use Redis (8.10.3-alpine) for redirect cache and rate-limit state, secured via password authentication. Use RabbitMQ (4.4.0-management-alpine) as the dedicated analytics message broker. Run a separate worker process to consume events.
- **Alternatives considered:**
  - BullMQ + Redis: simpler local stack and fewer containers; strong candidate if simplicity becomes more important than learning a distinct broker. Rejected at M0 review because RabbitMQ provides true separation of concerns, native AMQP acknowledgements, publisher confirms, and distinct learning value.
  - SQS: not chosen because AWS runtime is excluded and a cloud queue would violate the local-only baseline without an emulator.
  - Kafka: rejected as unnecessary complexity for the event volume and learning goals of the initial product.
- **Consequences:** Clear separation of cache and broker responsibilities; additional local service and operational learning. Need to configure durability, publisher confirms, retries, DLQ, credentials and health checks deliberately. Ports bound strictly to loopback `127.0.0.1` and credentials supplied via environment variables for local security.
- **Revisit when:** If RabbitMQ setup/maintenance materially delays the project, compare BullMQ on reliability, failure semantics, educational value and runtime complexity. Revisit with real evidence, not preference alone.
