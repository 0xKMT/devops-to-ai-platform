# Week 11: AIOps Incident Triage in Shadow Mode

## Outcome

Produce an evidence-backed incident summary and remediation draft without alerting or changing a live system.

## Timebox

- Reading: 2 hours
- Incident replay: 3-4 hours
- Evaluation and review: 1-2 hours

## Learn

- Signals, symptoms, causes and contributing factors.
- Timeline construction and change correlation.
- Facts, inferences and unknowns.
- Shadow mode and human escalation.
- Draft remediation versus authorized action.

## References

- [Google SRE Book: Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/)
- [Google SRE Book: Managing Incidents](https://sre.google/sre-book/managing-incidents/)
- [VersusControl: AI Agent for Monitoring](https://github.com/VersusControl/devops-ai-guidelines/tree/main/04-ai-agent-for-monitoring)

## Hands-on exercise

Create or sanitize one incident package containing alerts, metrics, logs, traces, a deployment event and a runbook. Replay it through the assistant and produce:

- Incident timeline.
- Evidence-backed impact summary.
- Ranked hypotheses.
- Missing evidence requests.
- Draft remediation and rollback plan.

## Commit evidence

- Incident fixture package.
- Expected timeline and findings.
- Assistant output and human review.
- Shadow-mode evaluation report.
- Weekly progress entry.

## Done checklist

- [ ] Facts, inferences and unknowns are separate.
- [ ] Every hypothesis cites evidence.
- [ ] Deployment correlation is not claimed as causation without proof.
- [ ] No alert, ticket, command or remediation is sent externally.
- [ ] Human review records accepted and rejected findings.

## Reflection

Did the assistant reduce investigation time, improve evidence quality or merely generate a polished summary?
