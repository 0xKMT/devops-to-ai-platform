# devops-to-ai-platform
A hands-on learning journey from DevOps to LLMOps and AgentOps, with AIOps use cases and MLOps foundations.

## Career direction

```text
DevOps → LLMOps → AgentOps
                  ├── AIOps use cases
                  └── MLOps foundations
```

## Current status

- [ ] Define baseline and weekly learning schedule
- [ ] Complete first read-only DevOps AI assistant
- [ ] Build a small LLMOps/RAG project
- [ ] Build a bounded AgentOps/MCP project
- [ ] Document AIOps and MLOps experiments

## Repository structure

- `curriculum/` — executable 12-week curriculum and free learning resources
- `roadmap/` — learning roadmap and milestones
- `projects/` — hands-on projects and evidence
- `notes/` — technical notes and summaries
- `decisions/` — architecture and tool decisions
- `weekly-progress/` — weekly learning log

## Start here

1. Read the [12-week curriculum](curriculum/README.md).
2. Start [Week 1: AI task classification](curriculum/12-week-foundation/week-01.md).
3. Record progress with [the weekly template](weekly-progress/TEMPLATE.md).
4. Build evidence under [Read-only Ops Copilot](projects/read-only-ops-copilot/README.md).

## Safety principles

- Read-only before mutation.
- No production credentials or raw secrets in examples.
- Human approval before external actions.
- Every AI result should be evaluated against evidence.
- Prefer deterministic automation when an LLM is unnecessary.

## First milestone

Build a local read-only assistant that analyzes sanitized logs, Terraform plans, or runbooks and returns evidence-backed findings.
