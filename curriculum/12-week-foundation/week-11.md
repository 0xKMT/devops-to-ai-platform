# Week 11: Replay an AIOps Incident in Shadow Mode

## What you are building

This week you will replay a synthetic incident package through the Ops Copilot. The system will correlate recorded alerts, metrics, logs, traces, deployment events and runbooks, then draft a triage report for human review.

It operates in shadow mode:

```text
recorded incident evidence
          |
          v
read-only retrieval and correlation
          |
          v
facts + hypotheses + unknowns
          |
          v
deterministic validation and policy
          |
          v
human accept/reject/edit
          |
          v
simulation + audit only
```

## Outcome

By the end of this week, you can:

- Package multi-signal incident evidence with a shared timeline.
- Distinguish correlation from causal proof.
- Produce fact, inference and unknown sections with citations.
- Run AIOps assistance in shadow mode beside a human baseline.
- Measure unsupported claims, evidence coverage and triage usefulness.
- Preserve human disagreement as evaluation data.

## Dependency

You need:

- Week 5/6 RAG corpus and adversarial evaluation.
- Week 7 telemetry concepts.
- Week 8 MCP read-only tools.
- Week 9 bounded agent.
- Week 10 policy, approval, simulation and audit controls.

## Time plan

| Session | Activity | Time |
| --- | --- | ---: |
| 1 | Incident response and AIOps shadow-mode model | 1.25 hours |
| 2 | Build the recorded incident package | 1.5 hours |
| 3 | Implement replay and evidence correlation | 2 hours |
| 4 | Compare AI report with human baseline | 1.5 hours |
| 5 | Evaluate and reflect | 1 hour |
| **Total** |  | **7.25 hours** |

## Mental model

A timestamp relationship is not automatically a root cause:

```text
deployment happened at 10:02
latency rose at 10:05

fact: both events occurred
inference: deployment may be related
unknown: causal mechanism is not yet proven
```

The goal is decision support, not automated incident command or remediation.

## Required reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [Google SRE Book: Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/) | `Why Monitor?`; `Symptoms Versus Causes`; `The Four Golden Signals` | 35 minutes | Choose signals that support operator decisions and avoid confusing symptoms with causes. |
| [Google SRE Book: Managing Incidents](https://sre.google/sre-book/managing-incidents/) | Opening scenario; `Elements of Incident Management Process`; `A Recognized Command Post`; `Live Incident State Document` | 35 minutes | Structure incident state and preserve human operational ownership. |
| [Google SRE Workbook: Incident Response](https://sre.google/workbook/incident-response/) | `Putting Best Practices into Practice` → `Incident Response Training`, `Prepare Beforehand` and `Drills` | 25 minutes | Treat the replay as a controlled drill, not a production experiment. |

All resources are free.

## Optional reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [Google SRE Incident Management Guide](https://sre.google/resources/practices-and-processes/incident-management-guide/) | `Prepare for incidents`; `Respond to incidents`; `Recover and learn` | 20 minutes | See a concise end-to-end incident response model. |

## Skip for now

Skip anomaly-detection model training, alert deduplication platforms, live PagerDuty/Slack/Jira integration, production event streams, automated root-cause claims, autonomous rollback and production remediation.

## Incident package

Create one synthetic incident, `incident-001`, with:

```text
manifest.yaml
alerts.jsonl
metrics.jsonl
logs.jsonl
traces.jsonl
deployments.jsonl
expected-timeline.yaml
human-baseline.md
```

Every evidence item needs:

```json
{
  "evidence_id": "metric-017",
  "timestamp": "2026-09-07T10:05:00Z",
  "source_type": "metric",
  "service": "demo-api",
  "environment": "sandbox",
  "summary": "p95 latency crossed 900 ms",
  "sanitized": true
}
```

Use UTC timestamps and a shared service vocabulary.

## Expected triage report

```yaml
incident_id: incident-001
impact:
  summary: ...
  evidence_ids: []
timeline:
  - timestamp: ...
    event: ...
    evidence_ids: []
facts: []
hypotheses:
  - statement: ...
    confidence: low
    supporting_evidence_ids: []
    contradicting_evidence_ids: []
unknowns: []
next_diagnostic_checks: []
proposed_actions: []
policy_outcome: simulate_only
```

A hypothesis cannot appear under `facts`. A root cause remains unknown unless the fixture explicitly proves it.

## Target project structure

```text
projects/read-only-ops-copilot/
|-- fixtures/incidents/incident-001/
|   |-- manifest.yaml
|   |-- alerts.jsonl
|   |-- metrics.jsonl
|   |-- logs.jsonl
|   |-- traces.jsonl
|   |-- deployments.jsonl
|   |-- expected-timeline.yaml
|   +-- human-baseline.md
|-- scripts/
|   +-- run_incident_replay.py
|-- evals/
|   |-- datasets/week-11-incident.yaml
|   +-- reports/week-11-shadow-mode.md
+-- docs/
    +-- aiops-shadow-mode.md
```

## Hands-on lab

### Step 1: Write the human baseline first

Before running the copilot, spend at most 20 minutes reviewing the incident package manually. Record:

- Impact.
- Timeline.
- Confirmed facts.
- Top two hypotheses.
- Missing evidence.
- Next diagnostic checks.
- Any safe proposed action.
- Time spent.

This becomes a comparison point, not unquestionable ground truth.

### Step 2: Add multi-signal fixture tools

Extend local read-only lookup without adding arbitrary paths. You may:

- Add one `get_incident_evidence` tool accepting `incident_id` and bounded `source_type`.
- Or keep direct deterministic fixture loading inside the replay script.

Do not add one broad `run_query` or `read_any_file` tool.

### Step 3: Build the evidence timeline

Normalize timestamps and sort events. Preserve source type and evidence ID. If timestamps tie, do not invent an ordering.

Expected timeline excerpt:

```text
10:02 deploy-003  demo-api version changed to v2
10:05 metric-017  p95 latency crossed 900 ms
10:06 alert-001   latency alert fired
10:08 trace-009   downstream timeout observed
```

### Step 4: Run the bounded analysis

```bash
python projects/read-only-ops-copilot/scripts/run_incident_replay.py \
  --incident incident-001 --mode shadow
```

The run must:

1. Load only the allowlisted incident.
2. Retrieve relevant runbook sections.
3. Build a structured analysis.
4. Validate every evidence reference.
5. Apply deterministic policy.
6. Request human review.
7. Simulate any approved action.
8. Append audit events.

Expected terminal summary:

```text
mode=shadow
incident=incident-001
facts=4 hypotheses=2 unknowns=3
invalid_evidence_ids=0
unsupported_causal_claims=0
external_actions=0
terminal=AWAITING_HUMAN_REVIEW
```

### Step 5: Human-review the output

For each section, mark:

```text
accept | edit | reject
reason:
missing_evidence:
new_regression_case:
```

The human may reject a fluent answer. Preserve the original output and review result.

### Step 6: Compare with the baseline

Measure:

- Time to first structured draft.
- Evidence coverage.
- Unsupported factual or causal claims.
- Missing important events.
- Usefulness of next diagnostic checks.
- Human edit rate.

Do not claim reduced MTTR from one synthetic replay.

## Common mistakes

| Mistake | Fix |
| --- | --- |
| A deployment before an alert is declared root cause | Record temporal correlation as a hypothesis. |
| The AI report replaces the incident commander | Keep it as an reviewed shadow artifact. |
| All evidence is pasted into one prompt | Retrieve bounded evidence and preserve IDs. |
| Human edits overwrite the original | Keep original, review and final version separately. |
| Faster draft is called better triage | Measure factual support and operational usefulness too. |

## Artifacts to commit

- `projects/read-only-ops-copilot/fixtures/incidents/incident-001/*`
- `projects/read-only-ops-copilot/scripts/run_incident_replay.py`
- `projects/read-only-ops-copilot/evals/datasets/week-11-incident.yaml`
- `projects/read-only-ops-copilot/evals/reports/week-11-shadow-mode.md`
- `projects/read-only-ops-copilot/docs/aiops-shadow-mode.md`
- Sanitized replay and audit samples
- `notes/week-11-reflection.md`

## Evaluation

| Check | Required target |
| --- | ---: |
| Evidence IDs resolving to fixture data | 100% |
| Unsupported factual or causal claims | 0 |
| Expected critical timeline events included | At least 90% |
| Proposed actions passing policy before review | 100% |
| External alerts, tickets, messages or mutations | 0 |
| Human review recorded | 100% |

For usefulness, have the human score impact, timeline, hypotheses and next checks from 0-2. Record the score; do not make it a hard safety gate.

## Safety boundary

- Synthetic, local incident package only.
- Shadow mode only.
- No live alerts, production logs, raw customer data or production credentials.
- No Slack, Jira, PagerDuty, cloud, Kubernetes or Terraform writes.
- Correlation is never presented as proven causation.
- Human review precedes simulation; simulation never mutates an external system.

## Definition of Done

- [ ] The incident package contains all six evidence types and a manifest.
- [ ] A human baseline exists before AI replay.
- [ ] Every fact and timeline item cites valid evidence IDs.
- [ ] Hypotheses include uncertainty and contradictory evidence where available.
- [ ] The run terminates awaiting human review.
- [ ] Human accept/edit/reject decisions are preserved.
- [ ] Shadow-mode metrics are reported without an MTTR claim.
- [ ] No external integration or mutation occurs.

## Knowledge check

1. Why does temporal correlation not prove root cause?
2. What is shadow mode?
3. Why write the human baseline before seeing AI output?
4. What should happen to a human-rejected output?

Expected answers:

1. Another hidden factor may cause both events, and a mechanism has not been proven.
2. The system analyzes real-shaped recorded work but does not control the live process or execute actions.
3. It reduces anchoring and provides an independent comparison.
4. Preserve it with the review reason and add the failure to regression data.

## What you have achieved

You now have a safe AIOps use case: a read-only incident triage assistant that correlates evidence, states uncertainty, waits for a human and generates measurable shadow-mode artifacts.

## Reflection

Which sentence in your AI report would be most dangerous if an operator mistook a hypothesis for a fact, and how does your schema make that confusion visible?
