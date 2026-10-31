# Pulse engineering log

Record durable engineering discoveries here. Do not create an entry for every trivial edit; use it for non-trivial defects, security findings, performance experiments and dependency-failure experiments.

## Bug entry template

Create a dated file such as `BUG-2026-11-01-cache-invalidation-race.md` containing:

- **Summary / status**
- **Affected milestone and component**
- **Observed symptom**
- **Expected behaviour**
- **Reproduction steps and environment**
- **Relevant logs / stack traces** (redact secrets and personal data)
- **Root cause** and evidence confirming it
- **Fix** and alternatives considered
- **Regression test** (file/test name and command)
- **Remaining risk / follow-up**
- **Learning / interview takeaway**

## Experiment entry template

For load/failure experiments record:

- Hypothesis and question
- System version / commit
- Machine, OS, runtime and container resource limits
- Dataset and test scenario
- Tool/version, duration, VUs and workload shape
- Measurements (throughput, p50/p95/p99, errors, CPU/memory, DB/cache/queue signals)
- Result and interpretation
- Bottleneck evidence
- Changes made and before/after comparison
- Limitations and reproducibility steps

Never report invented or selectively misleading benchmark figures.
