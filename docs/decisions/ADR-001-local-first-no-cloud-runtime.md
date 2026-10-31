# ADR-001: Local-first runtime; no cloud dependency in the MVP

- **Status:** Accepted baseline — 31 October 2026
- **Context:** Pulse must demonstrate backend and system-design capability while remaining genuinely zero-cost. The owner has an AWS Cloud Practitioner certification but does not want cloud charges for a demo project.
- **Decision:** The MVP runs locally using Docker Compose and open-source components. AWS is not a runtime requirement. Any future cloud deployment requires explicit owner approval and a cost review. AWS service mappings may be documented as learning material but must not be represented as deployed infrastructure.
- **Alternatives considered:**
  - Deploy the entire stack to AWS: rejected for the current phase because of cost/eligibility uncertainty and unnecessary dependency.
  - Use only a hosted free-tier stack: rejected as the foundation because free-tier availability/limits can change and may require billing setup.
  - LocalStack/AWS emulation: optional future learning experiment, not necessary for MVP and not assumed to be fully free/feature-complete for every service.
- **Consequences:** Reproducible local demo and no cloud bill. No public always-on endpoint by default. Portfolio demonstration will use local run instructions, screenshots, a recording and measured test reports unless deployment is later approved.
- **Revisit when:** Owner explicitly authorizes a specific, cost-bounded cloud deployment after checking current pricing, free-tier eligibility, resource lifecycle and billing alerts.
