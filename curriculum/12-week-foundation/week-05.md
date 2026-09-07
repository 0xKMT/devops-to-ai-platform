# Week 5: Runbook RAG Foundations

## Outcome

Answer operational questions from a small versioned runbook corpus with source citations.

## Timebox

- Reading: 2 hours
- Corpus and retrieval experiment: 4 hours
- Evidence and review: 1-2 hours

## Learn

- Ingestion, chunking, embeddings and retrieval.
- Metadata and source identity.
- Top-k retrieval and context assembly.
- Retrieval quality versus generation quality.
- Source freshness and document ownership.

## References

- [Hugging Face: Advanced RAG](https://huggingface.co/learn/cookbook/advanced_rag)
- [VersusControl: SRE Agent Brain](https://github.com/VersusControl/devops-ai-guidelines/tree/main/06-sre-agent-brain)

## Hands-on exercise

Create a corpus of 5-10 sanitized or synthetic runbooks. Give every document an owner, version, service, environment and review date. Test at least ten questions and inspect the retrieved chunks before inspecting the generated answer.

## Commit evidence

- Sanitized runbook corpus or corpus manifest.
- Chunking and metadata decision record.
- Retrieval test set and results.
- Weekly progress entry.

## Done checklist

- [ ] Every chunk can be traced to a document and section.
- [ ] Retrieval and answer generation are evaluated separately.
- [ ] Answers cite sources or abstain.
- [ ] Stale and ownerless runbooks are identifiable.
- [ ] Raw production data is not added to the corpus.

## Reflection

Was the largest quality problem caused by retrieval, document quality or generation?
