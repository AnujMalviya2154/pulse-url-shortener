# ADR-003: PostgreSQL as the durable source of truth

- **Status:** Accepted baseline — 31 October 2026
- **Context:** Pulse stores users, links, ownership relationships, lifecycle state and analytics identities/aggregates. These require unique constraints, transactions and relational queries.
- **Decision:** Use PostgreSQL as the canonical durable database. Use Drizzle ORM for typed schema/query/migration management while retaining visibility into SQL and indexes.
- **Alternatives considered:**
  - MongoDB: viable document database, but relational ownership and integrity are a natural fit for PostgreSQL and its constraints/transactions.
  - SQLite: excellent for a smaller local application, but PostgreSQL better demonstrates the target database/concurrency/query skills and aligns with separate local services.
- **Consequences:** Relational integrity and mature indexing/query plans. Requires migration discipline, connection pooling and local DB lifecycle management.
- **Revisit when:** Concrete requirements demonstrate a better fit or local development complexity outweighs the learning/product value.
