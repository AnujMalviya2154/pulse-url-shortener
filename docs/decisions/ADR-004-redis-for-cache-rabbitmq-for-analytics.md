# ADR-004: Redis for cache; RabbitMQ for asynchronous analytics

- **Status:** Accepted baseline, validate during M0 review — 31 October 2026
- **Context:** Redirect resolution benefits from a hot key-value cache. Click analytics requires durable asynchronous messaging, retries and consumer acknowledgement. Combining both responsibilities in one Redis deployment would reduce services but couple cache eviction/memory pressure to queue semantics.
- **Decision:** Use Redis for redirect cache and rate-limit state. Use RabbitMQ as the dedicated analytics message broker. Run a separate worker process to consume events.
- **Alternatives considered:**
  - BullMQ + Redis: simpler local stack and fewer containers; strong candidate if simplicity becomes more important than learning a distinct broker. It couples queue and cache infrastructure to Redis.
  - SQS: not chosen because AWS runtime is excluded and a cloud queue would violate the local-only baseline without an emulator.
  - Kafka: rejected as unnecessary complexity for the event volume and learning goals of the initial product.
- **Consequences:** Clear separation of cache and broker responsibilities; additional local service and operational learning. Need to configure durability, publisher confirms, retries, DLQ, credentials and health checks deliberately.
- **Revisit when:** If RabbitMQ setup/maintenance materially delays the project, compare BullMQ on reliability, failure semantics, educational value and runtime complexity. Revisit with real evidence, not preference alone.
