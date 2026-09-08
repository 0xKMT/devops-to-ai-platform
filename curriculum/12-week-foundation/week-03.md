# Week 3: Build a Prompt Contract and Structured-Output Validator

## What you are building

This week you will build the first deterministic boundary in the Ops Copilot:

```text
model or recorded response
    -> JSON parsing
    -> JSON Schema validation
    -> semantic checks
    -> accept or reject
```

This is the first week that requires a small amount of Python. The validator runs entirely offline and does not call a model API.

## Outcome

By the end of this week, you can:

- Design a versioned JSON output contract.
- Explain the difference between valid JSON and schema-valid JSON.
- Validate recorded model outputs with Python.
- Reject missing fields, invalid enums and unexpected fields.
- Explain why a schema validates structure but cannot prove a claim is true.

## Dependency

You need the Week 2 operational fixtures, prompt observations and failure modes. You should already treat model output as untrusted input.

## Time plan

| Session | Activity | Time |
| --- | --- | ---: |
| 1 | JSON Schema mental model and reading | 1.5 hours |
| 2 | Design the prompt and output contract | 1.5 hours |
| 3 | Build the validator and fixtures | 2.5 hours |
| 4 | Run negative tests and document the result | 1.5 hours |
| **Total** |  | **7 hours** |

## Mental model

A prompt is a best-effort instruction:

```text
Please return these fields.
```

A schema is a machine-readable contract:

```text
Only these fields, types and values are accepted.
```

A validator enforces the contract:

```text
invalid output -> explicit rejection
```

A schema-valid output can still be factually wrong:

```json
{
  "severity": "critical",
  "summary": "Redis is unavailable"
}
```

JSON Schema can check the fields and types. It cannot determine whether Redis appeared in the evidence. Week 4 will evaluate groundedness and correctness.

## Required reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [JSON Schema: Creating your first schema](https://json-schema.org/learn/getting-started-step-by-step) | `Introduction to JSON Schema`; `Create a schema definition`; `Define properties`; `Validate JSON data against the schema` | 40 minutes | Learn schemas, properties, required fields and validation. |
| [Python jsonschema: Schema Validation](https://python-jsonschema.readthedocs.io/en/stable/validate/) | `The Basics`; `validate()`; `Draft202012Validator.validate()` | 20 minutes | Implement the local validator. |

## Optional reading

| Resource | Exact sections | Time | Note |
| --- | --- | ---: | --- |
| [OpenAI: Structured model outputs](https://developers.openai.com/api/docs/guides/structured-outputs) | `When to use Structured Outputs`; `Structured Outputs vs JSON mode` | 20 minutes | Documentation is free; do not call the paid API for this lab. |

## Skip for now

Skip OpenAI SDK examples, function calling, tool schemas, Pydantic, Zod, recursive schemas, external references, conditional JSON Schema, automatic repair and provider-specific structured-output APIs.

## Output contract

Use these minimum fields:

| Field | Type | Rule |
| --- | --- | --- |
| `schema_version` | string | Exact supported version, initially `1.0.0`. |
| `summary` | string | Must not be empty. |
| `severity` | enum | `info`, `warning`, `critical` or `unknown`. |
| `facts` | array | Each fact contains a statement and evidence IDs. |
| `hypotheses` | array | Possible explanations, not confirmed causes. |
| `unknowns` | array | Information that is missing. |
| `suggested_action` | string or null | Text only; never executable. |
| `requires_human_approval` | boolean | Informational field, not an approval decision. |
| `abstain_reason` | string or null | Reason the assistant cannot conclude. |

The root schema should include:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "additionalProperties": false
}
```

`additionalProperties: false` detects model-created fields such as:

```json
{
  "execute_command": "kubectl rollout restart deployment/api"
}
```

## Prompt contract v1

```text
You are a read-only operations analyst.

Analyze only the supplied evidence.

Return one JSON object matching schema version 1.0.0.

Rules:
- Facts must reference evidence IDs.
- Hypotheses must not be presented as confirmed causes.
- Use severity "unknown" when evidence is insufficient.
- Suggested actions are text only.
- Never claim an external action was executed.
- Return no Markdown and no text outside the JSON object.
```

When using a chat UI, the provider may not enforce API-level structured output. The local validator is still required.

## Target project structure

```text
projects/read-only-ops-copilot/
|-- prompts/
|   `-- ops-analysis-v1.md
|-- schemas/
|   `-- ops-analysis-v1.schema.json
|-- fixtures/
|   `-- model-outputs/
|       |-- valid-basic.json
|       |-- valid-abstention.json
|       |-- invalid-json.txt
|       |-- missing-summary.json
|       |-- invalid-severity.json
|       |-- wrong-evidence-type.json
|       |-- unexpected-field.json
|       |-- empty-summary.json
|       |-- unsafe-command-field.json
|       `-- misleading-but-valid.json
`-- scripts/
    `-- validate_outputs.py
```

## Hands-on lab

### Step 1: Prepare the local environment

From the repository root:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install "jsonschema==4.26.0"
```

Expected installation result:

```text
Successfully installed jsonschema-4.26.0
```

If a newer version is deliberately selected when doing the lesson, pin and record that exact version instead.

### Step 2: Create the schema

Start with:

- An object at the root.
- Explicit required fields.
- An enum for severity.
- Arrays for facts, hypotheses and unknowns.
- Minimum lengths for important strings.
- `additionalProperties: false` on bounded objects.

### Step 3: Create ten fixtures

Include two valid and eight negative or edge cases:

| Fixture | Expected result |
| --- | --- |
| `valid-basic.json` | Accept. |
| `valid-abstention.json` | Accept. |
| `invalid-json.txt` | Reject during JSON parsing. |
| `missing-summary.json` | Reject missing required field. |
| `invalid-severity.json` | Reject invalid enum. |
| `wrong-evidence-type.json` | Reject wrong type. |
| `unexpected-field.json` | Reject additional property. |
| `empty-summary.json` | Reject minimum-length violation. |
| `unsafe-command-field.json` | Reject additional property. |
| `misleading-but-valid.json` | Accept structure; flag for Week 4 semantic evaluation. |

### Step 4: Build the validator

The script must:

1. Load and verify the schema.
2. Read every fixture.
3. Parse JSON.
4. Validate against Draft 2020-12.
5. Compare the observed result with the expected result.
6. Print `PASS` or `REJECT` with the reason.
7. Exit non-zero if any observed result differs from expectation.

### Step 5: Add basic semantic checks

Add deterministic application rules after schema validation:

```text
facts are present
    -> each fact must contain at least one evidence ID

facts are absent and the cause is unknown
    -> abstain_reason must be present
```

Do not ask the model to decide whether its own output should bypass these checks.

### Step 6: Run the validator

```bash
python projects/read-only-ops-copilot/scripts/validate_outputs.py
```

Expected shape of the output:

```text
PASS   valid-basic.json
PASS   valid-abstention.json
REJECT invalid-json.txt: invalid JSON
REJECT missing-summary.json: required property missing
REJECT invalid-severity.json: value is not in enum
REJECT unexpected-field.json: additional property not allowed
REJECT unsafe-command-field.json: additional property not allowed

Expected results: 10/10
Accepted unsafe fixtures: 0
```

The exact wording can differ, but every case and rejection reason must be visible.

## Error handling

| Failure | Required behavior |
| --- | --- |
| Invalid JSON | Reject. |
| Missing required field | Reject. |
| Invalid enum | Reject. |
| Unexpected field | Reject. |
| Evidence with wrong type | Reject. |
| Invalid schema file | Fail the validator. |
| Unreadable fixture | Fail the validator. |
| Schema-valid but unsupported claim | Pass structural validation and send to Week 4 evaluation. |

Do not silently repair malformed output in Week 3. Silent repair hides the model failure and makes evaluation misleading.

## Artifacts to commit

- `projects/read-only-ops-copilot/prompts/ops-analysis-v1.md`
- `projects/read-only-ops-copilot/schemas/ops-analysis-v1.schema.json`
- `projects/read-only-ops-copilot/scripts/validate_outputs.py`
- `projects/read-only-ops-copilot/fixtures/model-outputs/`
- `notes/week-03-structured-output.md`
- `weekly-progress/week-03.md`

## Evaluation

| Test | Pass condition |
| --- | ---: |
| Valid fixtures accepted | 100% |
| Invalid fixtures rejected | 100% |
| Unexpected fields rejected | 100% |
| Unsafe command fields accepted | 0 |
| Repeatable validator result | 100% |
| Model or paid API dependency | 0 |

## Common mistakes

- Checking only `json.loads()` and calling the output validated.
- Allowing arbitrary additional fields.
- Leaving severity as an unconstrained string.
- Mixing evidence and hypotheses.
- Automatically repairing bad output without an audit record.
- Treating schema validity as factual correctness.
- Treating `requires_human_approval: false` as a real authorization decision.

## Safety boundary

- Suggested actions remain text and cannot execute.
- The validator has no shell, cloud or Kubernetes permissions.
- Approval and policy are not delegated to the model.
- Fixtures contain only local, synthetic or sanitized data.

## Definition of Done

- [ ] Version the prompt and schema.
- [ ] Use JSON Schema Draft 2020-12.
- [ ] Create at least ten fixtures with expected outcomes.
- [ ] Accept valid fixtures and reject invalid fixtures.
- [ ] Reject unexpected and unsafe command fields.
- [ ] Run the validator fully offline.
- [ ] Return non-zero when an expectation fails.
- [ ] Document schema-valid but factually wrong output as a remaining gap.
- [ ] Complete notes and the weekly progress entry.

## Knowledge check

1. How is valid JSON different from schema-valid JSON?
2. What does `additionalProperties: false` enforce?
3. Can JSON Schema detect hallucination?
4. Why should invalid output not be silently repaired?
5. Can a model-provided approval field authorize an operation?

Expected answers: valid JSON only satisfies syntax; schema-valid JSON also satisfies the contract; schemas do not verify truth; silent repair hides failures; the model cannot authorize an operation.

## What you have achieved

You have created the first deterministic LLMOps boundary:

```text
untrusted model output -> validated data or explicit rejection
```

Week 4 will test whether structurally valid output is grounded, useful and safe.

## Reflection

Which guarantees belong in the prompt, and which guarantees must be enforced outside the model?
