# Week 1: Classify DevOps Tasks for AI

## Outcome

Decide when to use deterministic automation, an LLM, RAG, an agent or a human-only process.

## Timebox

- Reading: 2 hours
- Exercise: 3 hours
- Evidence and review: 1-2 hours

## Learn

- Deterministic versus probabilistic systems.
- Hallucination, uncertainty and evidence boundaries.
- Blast radius and reversibility.
- Why an LLM should not replace a script or policy engine.

## References

- [Hugging Face LLM Course: Introduction](https://huggingface.co/learn/llm-course/en/chapter1/1)
- [Google SRE Book: Eliminating Toil](https://sre.google/sre-book/eliminating-toil/)
- [Google SRE Book: Effective Troubleshooting](https://sre.google/sre-book/effective-troubleshooting/)

## Hands-on exercise

Create a decision matrix for at least 20 DevOps tasks. Include examples such as log summarization, Terraform plan review, secret rotation, service restart, incident timeline creation and capacity analysis.

Use these columns:

| Task | Deterministic rule available? | Needs unstructured reasoning? | Needs external action? | Blast radius | Recommended approach | Human gate | Evidence required |
| --- | --- | --- | --- | --- | --- | --- | --- |

Classify each task as one of:

- Deterministic automation
- LLM analysis
- RAG-assisted analysis
- Bounded agent
- Human-only

## Commit evidence

- `notes/week-01-ai-task-classification.md`
- Optional ADR under `decisions/` explaining the first portfolio use case.
- One entry created from `weekly-progress/TEMPLATE.md`.

## Done checklist

- [ ] At least 20 tasks are classified.
- [ ] Every task includes a reason and evidence requirement.
- [ ] High-blast-radius tasks require a human gate.
- [ ] At least three tasks are rejected as unsuitable for an LLM.
- [ ] The first project use case is read-only and testable with sanitized data.

## Reflection

Which task initially looked like an AI problem but became simpler and safer as deterministic automation?
