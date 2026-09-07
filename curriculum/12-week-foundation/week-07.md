# Week 7: LLM Observability

## Outcome

Trace one analysis request across input handling, retrieval, model inference, validation and policy decisions.

## Timebox

- Reading: 2 hours
- Telemetry design: 3-4 hours
- Review: 1 hour

## Learn

- Traces, metrics, logs and context propagation.
- Correlation IDs and stage-level spans.
- Latency, errors, token usage and evaluation metrics.
- Sensitive-data redaction.
- Cardinality and cost risks.

## References

- [OpenTelemetry: Signals](https://opentelemetry.io/docs/concepts/signals/)
- [OpenTelemetry: Logs](https://opentelemetry.io/docs/specs/otel/logs/)
- [Prometheus: Overview](https://prometheus.io/docs/introduction/overview/)

## Hands-on exercise

Design telemetry for this request path:

```text
ingest -> sanitize -> retrieve -> infer -> validate -> policy -> audit
```

Define span names, safe attributes, metrics and structured log events. Include failures such as timeout, invalid schema, missing evidence and denied action.

## Commit evidence

- Telemetry specification.
- Example trace tree.
- Metric catalog with label-cardinality review.
- Redaction checklist.
- Weekly progress entry.

## Done checklist

- [ ] A correlation ID links all stages.
- [ ] Prompt, model, schema and dataset versions are observable.
- [ ] Raw prompts and secrets are excluded from telemetry by default.
- [ ] Success, failure, abstention and policy denial are distinguishable.
- [ ] High-cardinality labels are identified and removed.

## Reflection

Can an operator explain a bad result using telemetry without reading private input data?
