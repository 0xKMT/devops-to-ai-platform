# Architecture

## Context

The Read-only Ops Copilot is a local learning system. It receives sanitized operational evidence, uses bounded AI analysis and records reproducible results. It is not connected to production systems.

## Components

| Component | Responsibility | Trust boundary |
| --- | --- | --- |
| Fixture loader | Reads approved local fixtures | Never accepts arbitrary production paths |
| Sanitizer | Detects or removes sensitive values | Runs before model input and telemetry |
| Retriever | Selects relevant runbook sections | Retrieved text remains untrusted data |
| Analyzer | Produces structured findings | Output is untrusted until validated |
| Schema validator | Rejects malformed output | Deterministic control |
| Policy engine | Maps proposals to allow, deny, approval or simulation | Independent from the model |
| Approval recorder | Records a human decision | Cannot be written by the model itself |
| Simulator | Produces a preview without an external write | `external_write` remains false |
| Audit recorder | Stores versions, decisions and outcomes | Excludes raw secrets and unnecessary prompt data |

## Data flow

```text
fixture
  -> sanitize
  -> retrieve trusted metadata and untrusted document text
  -> analyze
  -> validate schema and evidence
  -> evaluate deterministic policy
  -> record human decision
  -> simulate proposal
  -> append audit event
```

## Failure behavior

- Invalid input: reject with a typed error.
- Sensitive input: redact or reject before inference.
- Retrieval returns nothing: require abstention.
- Model timeout or malformed output: fail closed and record the failure.
- Missing evidence: reject the operational claim.
- Mutation request: deny or simulate according to deterministic policy.
- Audit failure: do not proceed to simulation.

## Initial quality signals

- Schema-valid output rate.
- Evidence coverage.
- Correct abstention rate.
- Unsafe recommendation rate.
- Retrieval relevance.
- End-to-end latency.
- Policy denial and simulation counts.

## Security invariants

- No production credentials.
- No generic execution tools.
- No model-controlled authorization.
- No external write.
- All inputs, prompts, schemas, policies and model identifiers are versioned.
