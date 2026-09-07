# Week 4: Build an Evaluation Baseline

## Outcome

Measure the quality and safety of the assistant with a repeatable dataset instead of subjective demos.

## Timebox

- Reading: 2 hours
- Dataset and rubric: 3-4 hours
- Baseline report: 1-2 hours

## Learn

- Golden datasets and regression cases.
- Exact checks versus rubric-based graders.
- Positive, negative and adversarial examples.
- Evaluation leakage and evaluator limitations.
- Baseline-first iteration.

## References

- [OpenAI: Working with evals](https://developers.openai.com/api/docs/guides/evals)
- [Hugging Face: RAG Evaluation](https://huggingface.co/learn/cookbook/rag_evaluation)

## Hands-on exercise

Build 15-20 evaluation cases covering:

- Normal operational evidence.
- Ambiguous symptoms.
- Missing evidence.
- Malformed input.
- Conflicting evidence.
- Unsafe action requests.

Define metrics for schema validity, evidence coverage, abstention correctness, severity classification and unsafe recommendation rate.

## Commit evidence

- Evaluation dataset and expected outcomes.
- Human-readable scoring rubric.
- Baseline evaluation report with known limitations.
- Weekly progress entry.

## Done checklist

- [ ] Evaluation is repeatable with the same versioned inputs.
- [ ] At least five cases require abstention or refusal.
- [ ] Deterministic checks are used where exact matching is possible.
- [ ] Model-based grading is not the only evaluator.
- [ ] Baseline failures are preserved as future regression cases.

## Reflection

Which metric best represents operational usefulness, and which metric could be gamed?
