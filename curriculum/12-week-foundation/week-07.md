# Week 7: Instrument the Copilot with Local Telemetry

## What you are building

This week you will make the RAG request path observable. One local request will produce correlated traces, metrics and structured logs without sending telemetry to an external service.

```text
request
  +-- sanitize
       +-- retrieve
            +-- assemble_context
                 +-- infer_or_replay
                      +-- validate
                           +-- policy
                                +-- audit
```

## Outcome

By the end of this week, you can:

- Map the AI request lifecycle into meaningful spans.
- Record latency, failures, abstentions and policy outcomes as metrics.
- Correlate logs with a request and trace ID.
- Avoid raw prompts, source text and secrets in telemetry.
- Diagnose a successful, invalid and abstaining request locally.
- Define an initial service-level view without pretending it is a production SLO.

## Dependency

You need:

- Week 5 RAG request path.
- Week 6 evaluation and safety classifications.
- Familiarity with logs, metrics and traces from DevOps work.

## Time plan

| Session | Activity | Time |
| --- | --- | ---: |
| 1 | Signals, golden signals and naming | 1.5 hours |
| 2 | Instrument traces | 2 hours |
| 3 | Add metrics and safe structured logs | 1.5 hours |
| 4 | Run three scenarios and write an observability note | 1.5-2 hours |
| **Total** |  | **6.5-7 hours** |

## Mental model

Observability data answers different questions:

| Signal | Question |
| --- | --- |
| Trace | Where did this request spend time or fail? |
| Metric | How often and how much across many requests? |
| Log | What discrete event happened with which safe metadata? |

Telemetry is also a data-exfiltration surface. Store identifiers and bounded metadata, not the full prompt or retrieved evidence.

## Required reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [OpenTelemetry: Signals](https://opentelemetry.io/docs/concepts/signals/) | Signal list and summaries for `Traces`, `Metrics`, `Logs` and `Baggage` | 20 minutes | Refresh the role of each signal. |
| [OpenTelemetry Python: Getting Started by Example](https://opentelemetry.io/docs/languages/python/getting-started/) | `Instrumentation`; `Run the instrumented app`; `Add manual instrumentation to automatic instrumentation` → `Traces` and `Metrics` | 45 minutes | Implement local console telemetry with the official Python SDK. |
| [Prometheus: Metric and label naming](https://prometheus.io/docs/practices/naming/) | `Metric names`; `Labels` | 20 minutes | Avoid ambiguous units and high-cardinality labels. |
| [Google SRE Book: Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/) | `The Four Golden Signals` only | 20 minutes | Connect latency, traffic, errors and saturation to the AI request path. |

All resources and the lab are free. No observability SaaS account is required.

## Skip for now

Skip deploying an OpenTelemetry Collector, Jaeger, Tempo, Prometheus server, Grafana dashboards, production sampling, vendor-specific LLM tracing, distributed context propagation, alert paging and formal production SLO commitments.

## Telemetry contract

Use bounded, low-cardinality attributes:

```text
request_id
prompt_version
schema_version
dataset_version
model_source = local | recorded | existing-chat
stage
status
error_type
policy_outcome
```

Do not record:

- Raw prompt or response text.
- Retrieved chunk text.
- User, email, customer or incident identifiers.
- Secrets, tokens or internal URLs.
- A unique request ID as a metric label.

## Target project structure

```text
projects/read-only-ops-copilot/
|-- observability/
|   |-- telemetry-contract.md
|   +-- sample-traces/
|       |-- success.json
|       |-- invalid-output.json
|       +-- abstention.json
|-- scripts/
|   +-- run_observed_request.py
+-- docs/
    +-- observability-baseline.md
```

## Hands-on lab

### Step 1: Draw the request lifecycle

Define one root span and child spans:

```text
ops_copilot.request
sanitize.input
retrieve.runbooks
assemble.context
infer.response
validate.schema
validate.citations
policy.evaluate
audit.append
```

Each span must have a clear start, end and status. Do not create a span for every token or log line.

### Step 2: Configure local console export

Install the OpenTelemetry API and SDK in the project environment. Configure a console span exporter so no telemetry leaves the machine.

Run:

```bash
python projects/read-only-ops-copilot/scripts/run_observed_request.py \
  --fixture normal-001
```

Expected trace shape:

```text
trace ops_copilot.request status=OK
  span sanitize.input status=OK
  span retrieve.runbooks status=OK top_k=3
  span infer.response status=OK model_source=recorded
  span validate.schema status=OK
  span policy.evaluate status=OK policy_outcome=allow_read
```

Exact exporter formatting can differ. Verify parent-child relationships and attributes.

### Step 3: Add metrics

Implement at least:

```text
ops_copilot_requests_total
ops_copilot_request_duration_seconds
ops_copilot_stage_duration_seconds
ops_copilot_validation_failures_total
ops_copilot_abstentions_total
ops_copilot_policy_denials_total
```

Use bounded labels such as `stage`, `status` and `error_type`. Do not label by prompt text, chunk ID, request ID or user.

### Step 4: Add structured logs

Emit JSON logs containing safe correlation fields:

```json
{
  "event": "validation.completed",
  "request_id": "req-demo-001",
  "trace_id": "recorded-trace-id",
  "schema_version": "1.0.0",
  "status": "failed",
  "error_type": "missing_required_field"
}
```

Use a redaction test containing a fake token. The token must not appear in console output, sample traces or logs.

### Step 5: Run three scenarios

Run:

1. Successful grounded answer.
2. Schema-invalid recorded response.
3. Missing-evidence response that abstains.

For each scenario, answer:

- Which span shows the outcome?
- Which metric changes?
- Which safe log event confirms it?
- Can you identify the request without recording its content?

### Step 6: Define an observability baseline

Document measurement definitions:

| Measure | Definition |
| --- | --- |
| Request latency | Root span duration from sanitized input to audit append. |
| Validation failure rate | Failed validation requests divided by all requests. |
| Abstention rate | Requests with explicit insufficient-evidence outcome divided by all requests. |
| Policy denial rate | Requests denied by deterministic policy divided by all requests. |

These are local baseline indicators, not production SLOs.

## Common mistakes

| Mistake | Fix |
| --- | --- |
| Logging the entire prompt for debugging | Store fixture ID and version; keep content in controlled test fixtures. |
| Using request ID as a metric label | Put it in traces and logs only. |
| Recording only model latency | Trace retrieval, validation, policy and audit too. |
| Marking abstention as a system error | Track it as a product outcome unless execution failed. |
| Treating traces as evaluation | Telemetry explains behavior; Week 4/6 evals judge quality. |

## Artifacts to commit

- `projects/read-only-ops-copilot/observability/telemetry-contract.md`
- `projects/read-only-ops-copilot/observability/sample-traces/*.json`
- `projects/read-only-ops-copilot/scripts/run_observed_request.py`
- `projects/read-only-ops-copilot/docs/observability-baseline.md`
- `notes/week-07-reflection.md`

## Evaluation

Run the three scenarios and require:

| Check | Target |
| --- | ---: |
| Requests with a root trace and stage spans | 3/3 |
| Logs correlated by request and trace ID | 3/3 |
| Validation failure represented in telemetry | 1/1 |
| Abstention represented without system-error status | 1/1 |
| Fake-secret occurrences in telemetry | 0 |
| Unbounded metric-label values | 0 |
| External telemetry destinations | 0 |

## Safety boundary

- Console export only.
- Sanitized fixtures only.
- Telemetry never stores raw model input, output or retrieved text.
- No production trace backend, credentials or live traffic.
- Observability does not grant the model any additional tool or data access.

## Definition of Done

- [ ] The request lifecycle has a documented span map.
- [ ] Three request scenarios produce correlated local telemetry.
- [ ] Metric names include clear units or `_total` where appropriate.
- [ ] Metric labels are bounded.
- [ ] The fake-secret redaction test passes.
- [ ] Validation, abstention and policy outcomes are distinguishable.
- [ ] The baseline defines each measure precisely.
- [ ] No external exporter is configured.

## Knowledge check

1. Why should request ID be a log field but not a metric label?
2. Which span should contain end-to-end latency?
3. Is abstention always an error?
4. Why is raw prompt logging risky even in an observability system?

Expected answers:

1. It is useful for correlation but creates unbounded metric cardinality.
2. The root request span.
3. No. It can be the correct safe product behavior.
4. Prompts can contain secrets, customer data or malicious content and telemetry has broad access.

## What you have achieved

You now have a locally observable AI request path. You can find where a request failed, count important outcomes and correlate evidence without leaking the content being analyzed.

## Reflection

If latency doubles, which telemetry lets you distinguish model delay from retrieval or validation delay, and what information must still come from evaluation rather than observability?
