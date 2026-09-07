# Week 2: LLM Fundamentals and Failure Modes

## Outcome

Explain how LLM behavior affects reliability in an operational workflow and identify unsupported claims in model output.

## Timebox

- Reading: 2 hours
- Experiment: 3-4 hours
- Evidence and review: 1 hour

## Learn

- Tokens, context windows and inference.
- Messages, instructions and context.
- Sampling and output variability.
- Hallucination, stale knowledge and missing evidence.
- Why confidence language is not calibrated probability.

## References

- [Hugging Face LLM Course: Transformer models](https://huggingface.co/learn/llm-course/en/chapter1/1)
- [Hugging Face Agents Course: LLMs](https://huggingface.co/learn/agents-course/en/unit1/what-are-llms)

## Hands-on exercise

Prepare five sanitized log or Terraform-plan fixtures. Run three prompt variants against the same fixtures:

1. A vague request.
2. A request with a role, goal and output format.
3. A request requiring quoted evidence and explicit `unknown` values.

Record output variation, unsupported claims, missing evidence and useful findings. Use an existing chat product, a local model or recorded responses; no paid API is required.

## Commit evidence

- `projects/read-only-ops-copilot/fixtures/` with sanitized inputs.
- `notes/week-02-llm-failure-modes.md` with the comparison table.
- Weekly progress entry.

## Done checklist

- [ ] Five fixtures and three prompt variants are evaluated.
- [ ] Raw secrets, account IDs and customer data are absent.
- [ ] Facts, inferences and unsupported claims are labeled separately.
- [ ] At least three recurring failure modes are documented.
- [ ] No model output is treated as an executable instruction.

## Reflection

Which improvement came from better context, and which failure still requires a deterministic control?
