# Pulse — security checklist and threat register

**Version:** 0.1 (M0 baseline)
**Status:** All implementation controls pending; this document is a plan, not evidence of completed security work.

## Status key

- `[ ]` Not implemented / not verified
- `[-]` In progress
- `[x]` Implemented and verified with linked evidence
- `[!]` Accepted risk or explicit deferral requiring owner sign-off

Every control marked complete must include evidence: test name/path, command, review record or experiment. A checklist tick without evidence is not completion.

## 1. Identity and session security

| ID | Threat / control | Planned safeguard | Verification evidence | Status |
|---|---|---|---|---|
| AUTH-01 | Password disclosure | Store only a modern password hash using an approved, maintained library and reviewed parameters | Hashing tests; DB inspection confirms no plaintext | [ ] |
| AUTH-02 | Credential stuffing / brute force | Rate-limit login and registration using appropriate IP/account buckets; avoid account enumeration | Rate-limit integration tests; consistent auth errors | [ ] |
| AUTH-03 | Session theft | Secure, HttpOnly, SameSite cookie policy; rotate/invalidate sessions on sign-in/out and sensitive changes as designed | Cookie assertions; session invalidation tests | [ ] |
| AUTH-04 | CSRF | Choose and implement CSRF protection consistent with cookie/auth/origin model; validate Origin/Referer where appropriate | Cross-origin mutation tests | [ ] |
| AUTH-05 | Session fixation / stale sessions | Regenerate session identity on authentication and expire/revoke sessions deliberately | Session lifecycle tests | [ ] |
| AUTH-06 | Secret leakage | Keep secrets in ignored local env files; only placeholders in `.env.example`; redact auth headers/cookies in logs | Secret scan and log review | [ ] |

## 2. Authorization and data isolation

| ID | Threat / control | Planned safeguard | Verification evidence | Status |
|---|---|---|---|---|
| AUTHZ-01 | BOLA/IDOR on links | Every private link query/update/delete scoped by authenticated owner ID | Cross-user API tests for every operation | [ ] |
| AUTHZ-02 | Analytics disclosure | Analytics queries join/scope to authenticated owner; no client-supplied owner identity trusted | Cross-user analytics tests | [ ] |
| AUTHZ-03 | Admin abuse/takedown privilege escalation | Separate privileged role/capability; audit all disable/takedown actions | Role matrix and negative permission tests | [ ] |
| AUTHZ-04 | Mass assignment | Explicit request schemas and field allowlists; never spread arbitrary request bodies into DB updates | Schema/unit tests | [ ] |

## 3. URL and redirect abuse

| ID | Threat / control | Planned safeguard | Verification evidence | Status |
|---|---|---|---|---|
| URL-01 | Dangerous schemes | Accept only valid `http:` and `https:` URLs; reject `javascript:`, `data:`, `file:`, malformed and protocol-relative inputs | URL validation table-driven tests | [ ] |
| URL-02 | Parser ambiguity | Parse with standard URL parser; define normalization and hostname/port handling; reject credentials in URLs unless explicitly justified | Edge-case tests | [ ] |
| URL-03 | Phishing/malware redirect abuse | Link-creation rate limits, report/takedown pathway, disabled-link state and clear abuse policy; do not claim malware detection | Abuse workflow tests/documented response process | [ ] |
| URL-04 | Server-side request forgery | MVP must not fetch destination URLs from the server (no previews, scraping, expansion, webhooks). Future fetch feature requires separate SSRF threat model | Code review confirms no outbound destination fetch; ADR for any future addition | [ ] |
| URL-05 | Alias route collisions | Reserve system paths and enforce allowed alias alphabet/length and uniqueness | Alias validation tests | [ ] |
| URL-06 | Open redirect expectations | Public redirect is intentional product behaviour; document this clearly, avoid using Pulse links for auth callbacks, and add abuse/takedown controls | Product/security review | [ ] |

## 4. API and application security

| ID | Threat / control | Planned safeguard | Verification evidence | Status |
|---|---|---|---|---|
| API-01 | SQL injection | Drizzle/parameterized queries; no user input interpolated into raw SQL | Static review + malicious-input tests | [ ] |
| API-02 | XSS | Render titles/referrers as text; no unsafe raw HTML; encode output by context | UI tests and source review | [ ] |
| API-03 | Request abuse / resource exhaustion | Bound payload size, pagination, search length, query range and request timeouts | Boundary/load tests | [ ] |
| API-04 | Rate-limit bypass | Separate limits for auth, link creation and sensitive mutations; trusted proxy configuration reviewed | Proxy/IP tests | [ ] |
| API-05 | CORS misconfiguration | Explicit allowed origins; no wildcard credentialed CORS | Config tests / response-header checks | [ ] |
| API-06 | Security header gaps | Helmet or equivalent reviewed policy; CSP and other headers tuned to actual frontend needs | Header integration tests | [ ] |
| API-07 | Verbose errors | Generic external error responses; detailed but sanitized server diagnostics | Error-path tests and log review | [ ] |
| API-08 | Dependency compromise | Lockfile committed; dependency audit/updates in CI; avoid unnecessary packages | CI report and dependency review | [ ] |

## 5. Redis and cache security/correctness

| ID | Threat / control | Planned safeguard | Verification evidence | Status |
|---|---|---|---|---|
| CACHE-01 | Stale disabled/deleted link remains redirectable | Invalidate after committed mutation; bounded TTL and explicit failure telemetry | Disable/edit/delete propagation tests | [ ] |
| CACHE-02 | Cache poisoning / wrong key mapping | Namespaced keys, server-generated values, strict schema on deserialization | Cache mapping tests | [ ] |
| CACHE-03 | Redis outage | PostgreSQL fallback and bounded DB load strategy | Redis-stop integration test | [ ] |
| CACHE-04 | Redis exposure | Bind local service privately in Compose; use credentials/network controls appropriate to environment | Compose/config review | [ ] |
| CACHE-05 | Cache stampede | Consider request coalescing/jitter/TTL strategy if measurements show stampede risk | Concurrent cold-key test | [ ] |

## 6. RabbitMQ and worker security/correctness

| ID | Threat / control | Planned safeguard | Verification evidence | Status |
|---|---|---|---|---|
| MQ-01 | Forged/malformed messages | Validate event schema and version at consumer boundary; reject unknown/invalid payloads safely | Consumer schema tests | [ ] |
| MQ-02 | Duplicate deliveries | Stable event ID and database uniqueness/idempotent processing | Duplicate replay test | [ ] |
| MQ-03 | Message loss on broker/process failure | Durable topology, persistence and publisher-confirm strategy; document residual publish window | Broker restart/failure experiments | [ ] |
| MQ-04 | Infinite retry loop | Bounded retry with backoff and dead-letter queue | Poison-message test | [ ] |
| MQ-05 | Queue flooding | Bound publish rate, event size, consumer concurrency and queue retention; monitor depth/lag | Load/flood test | [ ] |
| MQ-06 | Over-privileged broker credentials | Separate least-privilege credentials/vhosts where practical; no public broker exposure | Compose and config review | [ ] |
| MQ-07 | Worker crash after DB write before ack | Idempotency prevents duplicate count after redelivery | Crash-window integration test | [ ] |

## 7. Database and data privacy

| ID | Threat / control | Planned safeguard | Verification evidence | Status |
|---|---|---|---|---|
| DB-01 | Excessive DB privileges | Dedicated app credentials with minimum required permissions; separate migration role if appropriate | Role/permission inspection | [ ] |
| DB-02 | Data loss | Document local volume/backup limitations; define backup approach before any hosted use | Recovery drill if applicable | [ ] |
| DB-03 | Raw IP collection | Do not store raw IP in initial analytics model; rate-limit IP may be transient operational state | Schema and payload review | [ ] |
| DB-04 | Referrer/UA personal data | Minimize, strip query/fragment where practical, aggregate/coarsen and document retention | Event schema/privacy review | [ ] |
| DB-05 | Unbounded event retention | Define and implement retention/deletion/aggregation policy before public release | Retention test/job evidence | [ ] |
| DB-06 | Migration mistakes | Versioned migrations, review destructive changes, test upgrade path on disposable DB | Migration CI test | [ ] |

## 8. Infrastructure and operations

| ID | Threat / control | Planned safeguard | Verification evidence | Status |
|---|---|---|---|---|
| OPS-01 | Secrets committed | `.env` ignored; `.env.example` contains placeholders only; secret scanning | Git history/secret scan | [ ] |
| OPS-02 | Container privilege/exposure | Minimal images, non-root runtime where practical, only required ports published | Dockerfile/Compose review | [ ] |
| OPS-03 | Missing health/readiness | Explicit health checks and startup dependencies; do not confuse liveness with readiness | Container restart/readiness tests | [ ] |
| OPS-04 | Log injection / sensitive logs | Structured logs, sanitized untrusted fields, no secrets/session tokens | Log inspection tests | [ ] |
| OPS-05 | Unobserved failures | Metrics/logs for cache, DB, queue, worker, auth and rate limit behaviour | Dashboard or queryable local telemetry evidence | [ ] |
| OPS-06 | Unreviewed cloud cost | No cloud resources without explicit owner approval and cost review | No cloud resource requirement in setup docs | [ ] |

## 9. Security release gate

Before any public demo or external exposure:

- [ ] All P0 authentication, authorization, URL validation, secret handling and API controls are verified.
- [ ] Cross-user access tests cover all private link and analytics routes.
- [ ] Invalid URL schemes and alias collisions are tested.
- [ ] Abuse reporting/takedown behaviour is documented and usable.
- [ ] Dependency and secret scans have been reviewed.
- [ ] Error responses and logs have been checked for sensitive data leakage.
- [ ] Redis and RabbitMQ failure/recovery paths are tested.
- [ ] Analytics event retention and deletion implications are documented.
- [ ] Owner explicitly accepts any remaining risks.

## 10. Risk register (M0)

| Risk | Why it matters | Initial mitigation | Owner decision/status |
|---|---|---|---|
| URL shorteners are attractive to phishers | Abuse can damage users and project reputation | Rate limits, reporting, takedown, safe messaging; keep initial exposure controlled | Open — define MVP abuse process |
| Cache can serve stale destination/status | A disabled or edited link may continue redirecting briefly | Short TTL, invalidation and failure telemetry | Open — choose consistency target |
| Analytics can be lost around broker outage | Redirect path prioritizes availability | Explicit event-loss metric; evaluate outbox only if justified | Open — confirm durability target |
| Analytics metadata can contain personal data | Referrer/UA values may be sensitive | Data minimization, retention and aggregation | Open — approve event fields |
| RabbitMQ adds operational complexity | Extra local container and learning curve | Keep topology minimal; compare BullMQ alternative in ADR | Baseline selected; validate at M0 review |
| No hosted deployment | Demo is local unless separately arranged | Clear runbook, screenshots/demo recording and reproducible tests | Accepted for zero-cost baseline |
