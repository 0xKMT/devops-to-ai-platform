# Curriculum

This curriculum turns the high-level roadmap into weekly, evidence-driven work for a DevOps engineer moving toward AI Platform engineering.

## Career target

```text
Primary role:       AI Platform / LLMOps Engineer
Specialization:     AgentOps
Business use case:  AIOps
Supporting skill:   MLOps foundations
```

The first 12 weeks are a foundation sprint, not the entire career path.

## 12-18 month roadmap

| Phase | Time | Focus | Portfolio outcome |
| --- | --- | --- | --- |
| DevOps to AI baseline | Month 1 | Python minimum, APIs, probabilistic systems, evidence and safety | Sanitized fixtures and AI task decision matrix |
| LLMOps foundations | Months 1-3 | LLM behavior, prompts, structured output and evaluation | Read-only Ops Copilot v1 |
| LLMOps systems | Months 4-6 | RAG, embeddings, model gateway, observability, latency and cost | Runbook RAG with citations and regression tests |
| AgentOps | Months 7-10 | MCP, tool calling, state, scoped identity, approval, audit and recovery | Read-only MCP/Agent Gateway |
| AIOps use cases | Months 11-13 | Incident triage, signal correlation, change correlation and shadow mode | Incident Triage Copilot |
| MLOps foundations | Months 14-15 | Data/model lifecycle, experiments, registry, serving and drift | Small ML pipeline with lineage and monitoring |
| AI Platform capstone | Months 16-18 | Self-service platform, governance, SLOs, security and cost | AI Platform reference implementation |

## First 12 weeks

| Week | Topic | Main evidence |
| ---: | --- | --- |
| 1 | AI task classification | Decision matrix |
| 2 | LLM fundamentals and failure modes | Prompt experiment report |
| 3 | Prompt contracts and structured output | Output schema and validation cases |
| 4 | Evaluation baseline | Golden dataset and baseline report |
| 5 | Runbook RAG | Corpus manifest and retrieval tests |
| 6 | RAG evaluation and security | Adversarial evaluation report |
| 7 | LLM observability | Telemetry specification |
| 8 | Read-only MCP | Tool contracts and permission matrix |
| 9 | Agent fundamentals | Bounded agent flow and failure cases |
| 10 | AgentOps controls | Threat model, policy and audit schema |
| 11 | AIOps incident triage | Shadow-mode incident report |
| 12 | Capstone v1 | Demo, evaluation and security evidence |

## Weekly cadence

Use a 6-8 hour timebox:

- 2 hours: read only the assigned material.
- 3-4 hours: complete the hands-on exercise.
- 1 hour: create evaluation evidence.
- 30-60 minutes: write the weekly review and commit.

Do not attempt to finish every linked course. Read only the sections needed for the week's outcome.

## Learning loop

Every week follows the same loop:

```text
Learn -> Build -> Evaluate -> Document -> Commit -> Reflect
```

A week is complete only when another engineer can inspect the committed artifact and reproduce or challenge the conclusion.

## Safety boundary

All work in the first 12 weeks follows this path:

```text
local or sanitized input
    -> AI analysis
    -> deterministic policy check
    -> human approval record
    -> simulated action
    -> audit trail
```

- Do not use production credentials or raw secrets.
- Do not expose generic shell, SQL or HTTP execution to an agent.
- Keep external writes disabled.
- Treat model output as untrusted input.
- Require evidence or explicit abstention for operational conclusions.

## Lessons

- [Week 1](12-week-foundation/week-01.md)
- [Week 2](12-week-foundation/week-02.md)
- [Week 3](12-week-foundation/week-03.md)
- [Week 4](12-week-foundation/week-04.md)
- [Week 5](12-week-foundation/week-05.md)
- [Week 6](12-week-foundation/week-06.md)
- [Week 7](12-week-foundation/week-07.md)
- [Week 8](12-week-foundation/week-08.md)
- [Week 9](12-week-foundation/week-09.md)
- [Week 10](12-week-foundation/week-10.md)
- [Week 11](12-week-foundation/week-11.md)
- [Week 12](12-week-foundation/week-12.md)

See [Free learning resources](free-resources.md) for the curated source catalog.
