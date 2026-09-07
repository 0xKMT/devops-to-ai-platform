# Audit Examples

Audit examples must make a learning run explainable and replayable without storing sensitive content.

## Required event fields

- Event and correlation IDs.
- Timestamp.
- Fixture ID and hash.
- Prompt, schema, policy and dataset versions.
- Model or recorded-provider identifier.
- Tool names and result references.
- Validation outcome.
- Policy outcome.
- Human approval record when applicable.
- Simulated-action result.
- `external_write: false`.

## Event sequence

```text
input_accepted
input_sanitized
retrieval_completed
analysis_completed
schema_validated
policy_evaluated
approval_recorded
simulation_completed
request_closed
```

Store hashes or safe references instead of raw sensitive input. If audit recording fails, the workflow must stop before simulation.
