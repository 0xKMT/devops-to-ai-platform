# Week 9: Bounded Agent Fundamentals

## Outcome

Build or model a local agent loop that uses no more than two read-only tools and terminates predictably.

## Timebox

- Reading: 2 hours
- Agent exercise: 3-4 hours
- Failure testing: 1-2 hours

## Learn

- Thought-action-observation as a conceptual loop.
- Tool selection and observation handling.
- State versus long-term memory.
- Step, token and time budgets.
- Deterministic termination and fallback.

## References

- [Hugging Face Agents Course: Introduction to Agents](https://huggingface.co/learn/agents-course/en/unit1/introduction)
- [Hugging Face Agents Course: Tools](https://huggingface.co/learn/agents-course/en/unit1/tools)

## Hands-on exercise

Use two Week 8 tool contracts to investigate a fixture. Set a maximum step count, timeout, allowed tool list and final-response schema. Test tool failure, empty results, repeated calls and insufficient evidence.

Framework policy: select at most one agent framework for the exercise. Document the reason, but keep the evaluation and tool contracts independent from it.

## Commit evidence

- Agent flow and state diagram.
- Framework decision record.
- Failure-case fixtures and observed outcomes.
- Weekly progress entry.

## Done checklist

- [ ] The loop always reaches a defined terminal state.
- [ ] Maximum steps and timeout are enforced outside the model.
- [ ] Only allowlisted read-only tools are available.
- [ ] Tool errors do not become fabricated evidence.
- [ ] Repeated calls are detected or bounded.

## Reflection

What part of the workflow truly requires an agent loop instead of a fixed pipeline?
