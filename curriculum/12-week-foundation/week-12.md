# Week 12: Capstone v1 - Read-Only Ops Copilot

## Outcome

Demonstrate a local, reproducible and safely bounded DevOps AI workflow with evaluation and audit evidence.

## Timebox

- Integration and documentation: 4 hours
- Evaluation: 2 hours
- Demo and retrospective: 1-2 hours

## Golden path

```text
sanitized input
    -> AI analysis
    -> deterministic policy check
    -> human approval record
    -> simulated action
    -> audit trail
```

## Capstone package

- Architecture and trust boundaries.
- Sanitized fixtures and corpus manifest.
- Versioned prompt and output schema.
- Evaluation dataset and baseline.
- Read-only tool contracts.
- Policy, approval and audit records.
- Shadow-mode incident demo.
- Threat model and known limitations.

## Evaluation gate

Use these as learning thresholds, then adjust them after reviewing the baseline:

- At least 20 versioned evaluation cases.
- 100% of mutation attempts blocked before approval.
- 100% of external actions remain simulated.
- Every operational claim has evidence or explicit abstention.
- No production credential, raw secret or customer data in the repository.
- One request can be replayed from version and audit metadata.

## Commit evidence

- Completed project README.
- Architecture and threat-model documents.
- Evaluation report.
- Five-minute demo script.
- Retrospective with the next three learning priorities.
- Weekly progress entry.

## Done checklist

- [ ] A new reviewer can run or inspect the demo from repository instructions.
- [ ] The project clearly states scope and non-goals.
- [ ] Evaluation failures are documented rather than hidden.
- [ ] Security controls are enforced outside the LLM.
- [ ] The next phase is selected from observed gaps, not tool popularity.

## Reflection

Which evidence demonstrates readiness for deeper LLMOps, and which gaps must be resolved before adding more tools or autonomy?
