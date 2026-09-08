# Week 9: Build a Bounded Read-only Agent Loop

## What you are building

This week you will build a small agent runtime that can choose between two Week 8 read-only tools, observe their results and either continue or return a structured final answer.

The planner can use recorded responses. The runtime, tool allowlist and termination rules must be deterministic.

```text
goal
  |
  v
recorded/local planner -> proposed tool call or final answer
  |                           |
  v                           v
runtime validates          final validator
  |
  v
read-only MCP tool -> bounded observation -> next step
```

## Outcome

By the end of this week, you can:

- Explain the thought-action-observation loop without depending on a framework.
- Separate probabilistic planning from deterministic execution.
- Validate tool names and arguments before every call.
- Enforce maximum steps, timeout and duplicate-call limits.
- Preserve observations and terminal reasons in an audit trace.
- Make the agent fail closed when evidence or planner output is invalid.

## Dependency

You need:

- Week 8 MCP server and tests.
- Week 7 telemetry contract.
- Week 4/6 evaluation runner.

Use only `search_fixture_logs` and `get_runbook_section` this week. Keep `explain_saved_tf_plan` outside the agent allowlist to practice least privilege.

## Time plan

| Session | Activity | Time |
| --- | --- | ---: |
| 1 | Agent loop and tool concepts | 1.25 hours |
| 2 | Define planner messages and runtime states | 1.5 hours |
| 3 | Implement the bounded loop | 2 hours |
| 4 | Add failure and termination tests | 1.5 hours |
| 5 | Evaluate and reflect | 1 hour |
| **Total** |  | **7.25 hours** |

## Mental model

An agent is a system, not just an LLM:

```text
agent = planner + state + tools + runtime controls + evaluation
```

The planner may propose:

```json
{
  "type": "tool_call",
  "tool": "search_fixture_logs",
  "arguments": {
    "fixture_id": "logs-api-timeout",
    "query": "timeout",
    "limit": 5
  }
}
```

But only the runtime can validate and dispatch it. A model statement such as `policy_check_passed=true` has no authority.

## Required reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [Hugging Face Agents Course: What are Tools?](https://huggingface.co/learn/agents-course/en/unit1/tools) | `What are AI Tools?`; tool description, callable, arguments and outputs; `How do I give tools to an LLM?` | 30 minutes | Understand tools as typed capabilities exposed to a model. |
| [Hugging Face Agents Course: Thought-Action-Observation Cycle](https://huggingface.co/learn/agents-course/en/unit1/agent-steps-and-structure) | `The Core Components`; `The Thought-Action-Observation Cycle`; example `Thought`, `Action`, `Observation` and `Final Action` | 35 minutes | Learn the loop before introducing a framework. |
| [OWASP LLM06:2025 Excessive Agency](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/) | Opening definition and root causes; `Common Examples of Risks`; `Prevention and Mitigation Strategies` items 1-3 | 30 minutes | Connect excessive functionality, permissions and autonomy to runtime design. |

All resources are free. Do not run the Hugging Face framework exercises this week.

## Optional reading

| Resource | Exact section | Time | Why |
| --- | --- | ---: | --- |
| [Hugging Face Agents Course: Observe](https://huggingface.co/learn/agents-course/unit1/observations) | `How Are the Results Appended?` | 10 minutes | Clarify how tool results become the next bounded observation. |

## Skip for now

Skip LangGraph, smolagents, LlamaIndex agents, autonomous planning, browser tools, code execution, memory databases, multi-agent systems, write tools and production deployment.

## Runtime state machine

Use explicit states:

```text
START
  -> PLAN
  -> VALIDATE_PROPOSAL
  -> CALL_TOOL
  -> RECORD_OBSERVATION
  -> PLAN
  -> VALIDATE_FINAL
  -> COMPLETE

Any state -> DENIED | TIMEOUT | MAX_STEPS | INVALID_OUTPUT | TOOL_ERROR
```

Every run must end in exactly one terminal state.

## Target project structure

```text
projects/read-only-ops-copilot/
|-- agent/
|   |-- runtime.py
|   |-- planner.py
|   |-- state.py
|   +-- recorded_plans/
|-- tests/
|   +-- test_agent_runtime.py
|-- evals/
|   |-- datasets/week-09-agent.yaml
|   +-- reports/week-09-agent.md
+-- docs/
    +-- bounded-agent-design.md
```

## Hands-on lab

### Step 1: Define planner output schemas

Allow only two output types.

Tool call:

```json
{
  "type": "tool_call",
  "tool": "search_fixture_logs",
  "arguments": {}
}
```

Final answer:

```json
{
  "type": "final",
  "answer": {
    "summary": "...",
    "facts": [],
    "inferences": [],
    "unknowns": [],
    "evidence": []
  }
}
```

Set `additionalProperties: false` and reuse Week 3 validation practices.

### Step 2: Create a recorded planner

Store deterministic sequences for tests:

```text
step 1 -> search_fixture_logs
step 2 -> get_runbook_section
step 3 -> final
```

The recorded planner makes the agent loop testable without a paid API. A local model or existing ChatGPT subscription can be an optional adapter, not the test oracle.

### Step 3: Implement runtime controls

Configure:

```yaml
allowed_tools:
  - search_fixture_logs
  - get_runbook_section
max_steps: 3
wall_timeout_seconds: 10
max_observation_characters: 4000
max_duplicate_calls: 1
```

Before dispatch, verify:

1. Planner output matches its schema.
2. Tool is on the per-run allowlist.
3. Arguments match the MCP tool schema.
4. The same normalized call has not already repeated.
5. Time and step budgets remain.

### Step 4: Record a safe agent trace

```json
{
  "run_id": "agent-demo-001",
  "step": 2,
  "event": "tool.completed",
  "tool": "get_runbook_section",
  "argument_hash": "sha256:demo",
  "status": "ok",
  "observation_characters": 842
}
```

Store argument hashes or safe fixture IDs, not raw prompts and full observations.

### Step 5: Test termination behavior

Create at least eight cases:

| Case | Expected terminal state |
| --- | --- |
| two-tools-then-final | `COMPLETE` |
| direct-final | `COMPLETE` |
| unknown-tool | `DENIED` |
| invalid-arguments | `INVALID_OUTPUT` |
| repeated-call | `DENIED` |
| tool-not-found | `TOOL_ERROR` |
| never-final | `MAX_STEPS` |
| slow-planner | `TIMEOUT` |

Run:

```bash
python projects/read-only-ops-copilot/agent/runtime.py \
  --scenario two-tools-then-final
```

Expected shape:

```text
step=1 proposal=search_fixture_logs validation=pass
step=1 tool_status=ok observation_chars=640
step=2 proposal=get_runbook_section validation=pass
step=2 tool_status=ok observation_chars=921
step=3 proposal=final validation=pass
terminal=COMPLETE tool_calls=2
```

### Step 6: Evaluate the runtime

Add all eight cases to the local evaluator. The process must exit non-zero if an unknown tool executes, a step budget is exceeded without termination or a schema-invalid final answer is accepted.

## Common mistakes

| Mistake | Fix |
| --- | --- |
| The prompt says `never call unsafe tools` | Enforce a runtime allowlist independent of the prompt. |
| A loop ends only when the model says it is done | Add deterministic step and time budgets. |
| Tool errors are silently passed as facts | Mark typed errors and decide whether to terminate. |
| Full observations accumulate without bounds | Truncate safely and record that truncation occurred. |
| A framework hides the state machine | Implement the minimal loop directly first. |

## Artifacts to commit

- `projects/read-only-ops-copilot/agent/runtime.py`
- `projects/read-only-ops-copilot/agent/planner.py`
- `projects/read-only-ops-copilot/agent/state.py`
- `projects/read-only-ops-copilot/agent/recorded_plans/*`
- `projects/read-only-ops-copilot/tests/test_agent_runtime.py`
- `projects/read-only-ops-copilot/evals/datasets/week-09-agent.yaml`
- `projects/read-only-ops-copilot/evals/reports/week-09-agent.md`
- `projects/read-only-ops-copilot/docs/bounded-agent-design.md`
- `notes/week-09-reflection.md`

## Evaluation

| Check | Required target |
| --- | ---: |
| Expected terminal state | 8/8 |
| Unknown or non-allowlisted tools executed | 0 |
| Runs exceeding step or time budget | 0 |
| Duplicate calls executed beyond limit | 0 |
| Schema-invalid finals accepted | 0 |
| External or write actions | 0 |

## Safety boundary

- Only two named read-only MCP tools are available.
- No shell, browser, cloud, Kubernetes, Terraform execution or arbitrary file access.
- Model/planner output is untrusted.
- The runtime, not the model, owns budgets and dispatch.
- Tests use recorded or local planner responses and sanitized fixtures.

## Definition of Done

- [ ] The state machine and terminal states are documented.
- [ ] The recorded planner can drive a complete run.
- [ ] Tool and argument validation occurs before dispatch.
- [ ] Maximum steps, timeout, observation size and duplicate limits are enforced.
- [ ] All eight termination tests pass.
- [ ] Every run records a terminal reason.
- [ ] No non-allowlisted tool can execute.
- [ ] The evaluator fails closed on a hard safety violation.

## Knowledge check

1. Which part decides what tool might help?
2. Which part decides whether that tool call may execute?
3. Why is `max_steps` a safety control?
4. Why use a recorded planner in tests?

Expected answers:

1. The probabilistic planner.
2. Deterministic runtime validation and policy.
3. It bounds loops, cost, latency and repeated side effects.
4. It makes state and failure behavior reproducible without paid API dependence.

## What you have achieved

You now have a framework-independent agent runtime with a small capability set and deterministic stopping behavior. It can gather local evidence but cannot expand its own permissions.

## Reflection

Which controls would still protect the system if the planner proposed the worst possible valid-looking tool call?
