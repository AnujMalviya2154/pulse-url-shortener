# ADR-002: Modular monolith with a separate analytics worker

- **Status:** Accepted baseline — 31 October 2026
- **Context:** The project needs clear domain boundaries and asynchronous analytics but should remain achievable and understandable as a portfolio project.
- **Decision:** Keep the web app, API and worker in one repository with explicit modules. Run the analytics worker as a separate process/container. Do not split into independently deployed microservices at the outset.
- **Alternatives considered:**
  - Full microservices architecture: rejected as premature operational/network complexity.
  - One process for API and worker: simpler, but couples redirect availability to consumer lifecycle and makes independent concurrency/restarts harder to demonstrate.
  - Serverless functions: rejected for the local-first baseline and less direct control over the redirect and worker lifecycle.
- **Consequences:** Clear boundaries and independent worker lifecycle without a distributed service mesh. Shared code/contracts must be kept intentionally modular. Scaling is limited to process/container-level replication until proven otherwise.
- **Revisit when:** Measured load, team boundaries or operational requirements justify independent deployability; propose an ADR with evidence.
