# Evaluation Approach

## Evaluation layers

| Layer | Question | Preferred check |
| --- | --- | --- |
| Input | Is the fixture accepted and sanitized? | Deterministic validation |
| Retrieval | Were relevant sections retrieved? | Expected document and section IDs |
| Output | Does the response match the contract? | JSON Schema validation |
| Evidence | Are conclusions supported? | Evidence coverage plus human rubric |
| Abstention | Does the system stop when evidence is insufficient? | Expected outcome comparison |
| Policy | Are unsafe proposals denied or simulated? | Deterministic policy assertions |
| Workflow | Can the result be replayed? | Version and audit metadata check |

## Minimum case groups

- Normal cases.
- Ambiguous evidence.
- Missing evidence.
- Conflicting sources.
- Malformed model output.
- Prompt injection in retrieved text.
- Tool timeout or empty result.
- Requests for mutation or excessive permission.

## Baseline metrics

- Schema-valid output rate.
- Evidence coverage rate.
- Correct abstention rate.
- Unsafe recommendation rate.
- Retrieval hit rate for expected sources.
- Policy block rate for mutation attempts.

Evaluation failures remain in the dataset as regression cases. Model-based grading may supplement but never replace deterministic checks and human review.
