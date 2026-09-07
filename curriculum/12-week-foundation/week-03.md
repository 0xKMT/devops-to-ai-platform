# Week 3: Prompt Contracts and Structured Output

## Outcome

Define an output contract that downstream code can validate without trusting free-form model text.

## Timebox

- Reading: 1-2 hours
- Design and experiment: 4 hours
- Evidence and review: 1 hour

## Learn

- Prompt versioning and reproducibility.
- Structured output and JSON Schema.
- Required versus optional fields.
- Evidence references, uncertainty and abstention.
- Schema validation as a deterministic boundary.

## References

- [OpenAI Structured Outputs](https://developers.openai.com/api/docs/guides/structured-outputs)
- [OpenAI Prompt Engineering](https://developers.openai.com/api/docs/guides/prompt-engineering)

The documentation is free to read. Implement the exercise with recorded outputs if an API would incur cost.

## Hands-on exercise

Design an analysis contract containing at least:

- `summary`
- `severity`
- `evidence[]`
- `uncertainty[]`
- `suggested_action`
- `requires_human_approval`
- `abstain_reason`

Create ten outputs: valid, malformed, missing evidence, unknown severity and malicious text pretending to be a tool command. Pass them through a deterministic schema validator.

## Commit evidence

- Versioned prompt specification under the project docs.
- Output JSON Schema under the project evaluation area.
- Ten validation fixtures and expected results.
- Weekly progress entry.

## Done checklist

- [ ] Required fields and enums are explicit.
- [ ] Invalid output is rejected rather than repaired silently.
- [ ] Evidence points to an input location or runbook section.
- [ ] Missing evidence produces abstention.
- [ ] Suggested actions remain text and cannot execute directly.

## Reflection

Which guarantees belong in the prompt, and which guarantees must be enforced outside the model?
