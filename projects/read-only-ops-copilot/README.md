# Read-Only Ops Copilot

## Purpose

Build a local portfolio project that analyzes sanitized DevOps evidence, grounds findings in supplied sources and produces no external mutation.

## Golden path

```text
local or sanitized input
    -> AI analysis
    -> deterministic policy check
    -> human approval record
    -> simulated action
    -> audit trail
```

## In scope

- Sanitized logs, saved Terraform plans, synthetic alerts and runbooks.
- Structured analysis with evidence and abstention.
- Repeatable evaluation datasets.
- Read-only tool contracts.
- Simulated actions and local audit examples.

## Out of scope

- Production credentials or customer data.
- Live Slack, Jira, cloud or Kubernetes mutation.
- Arbitrary shell, SQL or HTTP execution.
- Autonomous remediation.
- Multi-agent orchestration.

## Milestones

| Weeks | Milestone |
| --- | --- |
| 1-2 | Use case selection, sanitized fixtures and failure analysis |
| 3-4 | Structured output and evaluation baseline |
| 5-6 | Runbook RAG and adversarial tests |
| 7 | Telemetry specification |
| 8-10 | Read-only MCP contracts and AgentOps controls |
| 11 | Shadow-mode incident triage |
| 12 | Reproducible capstone demonstration |

## Project evidence

- [Architecture](docs/architecture.md)
- [Fixture policy](fixtures/README.md)
- [Evaluation approach](evals/README.md)
- [Audit examples](audit-examples/README.md)

## Acceptance criteria

- At least 20 versioned evaluation cases.
- Every operational claim has evidence or explicit abstention.
- All mutation attempts are denied or simulated.
- No external write capability is available.
- One request can be replayed from version and audit metadata.
- Known failures and limitations are documented.
