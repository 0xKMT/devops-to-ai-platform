# AI Operations Glossary

Use this glossary as a living document. Add a source and an example when introducing a new term.

| Term | Working definition | Example in this repository |
| --- | --- | --- |
| DevOps | Practices that improve software delivery and operation through collaboration, automation and feedback | CI/CD, Terraform and operational evidence |
| MLOps | Practices for developing and operating ML systems across data, training, validation, serving and monitoring | The later mini ML lifecycle project |
| LLMOps | Operating applications that depend on LLMs, prompts, retrieval, evaluation, observability and model providers | Runbook RAG and Read-only Ops Copilot |
| Agent | A system in which a model can select tools and use observations across multiple steps toward a goal | A bounded incident-investigation loop |
| AgentOps | Controls and practices for operating agents, including tools, state, identity, approvals, audit and recovery | Read-only MCP/Agent Gateway |
| AIOps | Applying AI or ML to IT operations use cases | Shadow-mode incident triage |
| RAG | Retrieval-Augmented Generation: retrieving external context before generating an answer | Retrieving sanitized runbook sections |
| Evaluation | Repeatable measurement of behavior against datasets and criteria | Golden incident and log-analysis cases |
| Groundedness | Degree to which a conclusion is supported by supplied evidence | Findings cite fixture lines or runbook sections |
| Abstention | Explicitly declining to conclude when evidence or permission is insufficient | `abstain_reason` in the output contract |
| MCP | Open protocol connecting AI applications to data sources, tools and workflows | Read-only fixture and runbook tools |
| Shadow mode | Producing recommendations alongside a real or replayed workflow without taking action | Incident triage with no external write |
| Human approval | A recorded human decision required before a controlled action can proceed | Approval record before simulation |
| Deterministic policy | Non-LLM rules that decide whether a proposal is allowed, denied or simulated | Mutation requests always map to `simulate_only` or `deny` |
| Audit trail | Append-only evidence describing inputs, versions, decisions and outcomes | Replayable request records |
