# Week 6: RAG Evaluation and Security

## Outcome

Test whether RAG remains grounded when documents are missing, conflicting, stale or malicious.

## Timebox

- Reading: 2 hours
- Adversarial cases: 3-4 hours
- Evaluation report: 1-2 hours

## Learn

- Retrieval recall and answer groundedness.
- No-answer evaluation.
- Indirect prompt injection in retrieved content.
- Conflicting and stale sources.
- Trusted metadata versus untrusted document text.

## References

- [Hugging Face: RAG Evaluation](https://huggingface.co/learn/cookbook/rag_evaluation)
- [OWASP GenAI Security Top 10](https://genai.owasp.org/initiatives/top-10-for-llm-and-genai/)

## Hands-on exercise

Add adversarial documents and questions:

- A runbook containing instructions to ignore the system policy.
- Two runbooks with conflicting remediation steps.
- A stale runbook marked with an old review date.
- A question for which no runbook exists.
- A document containing fake credentials or command output.

Evaluate retrieval, citation, abstention and whether untrusted instructions influence suggested actions.

## Commit evidence

- Adversarial corpus additions.
- Expected safe behavior for each case.
- RAG security and evaluation report.
- Weekly progress entry.

## Done checklist

- [ ] Retrieved text is treated as data, not trusted instruction.
- [ ] Conflicting sources are surfaced explicitly.
- [ ] Missing evidence causes abstention.
- [ ] Stale sources are reported.
- [ ] No adversarial case can trigger an external action.

## Reflection

Which trust decision cannot be delegated to retrieval ranking or the LLM?
