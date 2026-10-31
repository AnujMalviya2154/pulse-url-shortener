# ADR-005: Cryptographically random short codes

- **Status:** Proposed baseline; finalize before M3
- **Context:** Short codes should be compact, difficult to enumerate and unique. Base62 describes an alphabet/encoding, not the mechanism that generates IDs.
- **Decision:** Use a cryptographically secure random generator to create Base62-like codes. Enforce uniqueness in PostgreSQL and retry collisions a bounded number of times. Choose exact initial length after documenting entropy/collision calculations and product constraints.
- **Alternatives considered:**
  - Sequential DB ID encoded as Base62: simple and compact, but predictable/enumerable and leaks creation order/volume.
  - Snowflake-style distributed IDs encoded as Base62: useful for distributed ID generation, but unnecessary complexity for the single-primary local-first design.
  - Random UUID string: strong randomness but longer URL representation than needed.
- **Consequences:** Unpredictable codes and simple architecture. Must handle rare collisions correctly at DB constraint boundary; code length affects collision probability and UX.
- **Revisit when:** Scale, multi-writer generation or custom alias constraints change materially.
