# Week 12: Package the Read-only Ops Copilot Capstone

## What you are building

This week you will integrate and package the work from Weeks 1-11 as a portfolio-ready capstone. You are not adding a new framework or production integration.

The final demo must show this complete golden path:

```text
sanitized/local incident
        |
        v
read-only AI analysis
        |
        v
schema and citation validation
        |
        v
deterministic policy
        |
        v
human approval
        |
        v
simulated action
        |
        v
audit trail + telemetry + eval report
```

## Outcome

By the end of this week, you can:

- Run one documented capstone workflow from a clean local environment.
- Explain architecture, trust boundaries and failure behavior.
- Prove quality and safety with at least 20 regression cases.
- Demonstrate the human approval and simulation boundary.
- Present measurable results without claiming production readiness.
- Give a five-minute portfolio walkthrough with reproducible evidence.

## Dependency

You need the artifacts from all prior weeks:

| Weeks | Required capability |
| --- | --- |
| 1-2 | Task framing, sanitized fixtures and prompt experiments |
| 3-4 | Structured output, validation and baseline evaluation |
| 5-6 | Local RAG, citations and adversarial regression |
| 7 | Local telemetry |
| 8 | Read-only MCP tools |
| 9 | Bounded agent runtime |
| 10 | Policy, approval, simulation and audit |
| 11 | AIOps shadow-mode incident replay |

If one dependency is incomplete, record it as a known gap. Do not hide it behind a manual demo.

## Time plan

| Session | Activity | Time |
| --- | --- | ---: |
| 1 | Freeze scope and architecture | 1 hour |
| 2 | Create one reproducible entrypoint | 1.5 hours |
| 3 | Consolidate and run 20+ evaluations | 2 hours |
| 4 | Failure injection and safety review | 1.5 hours |
| 5 | README, demo script and reflection | 2 hours |
| **Total** |  | **8 hours** |

This week is at the upper limit. If time is short, prioritize reproducibility, hard safety gates and honest documentation; skip visual polish.

## Mental model

A capstone is credible when another engineer can answer:

```text
What problem does it solve?
What evidence can it read?
What can it never do?
How do I run it?
How do I know it is correct enough?
How does it fail?
Where is human judgment required?
```

A passing demo is weaker evidence than a repeatable test suite plus visible failure cases.

## Required reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [Google SRE Book: Testing for Reliability](https://sre.google/sre-book/testing-reliability/) | Opening `Testing for Reliability`; `Relationships Between Testing and Mean Time to Repair`; `Testing Disaster`; `Conclusion` | 35 minutes | Frame tests as evidence that reduces uncertainty, not proof of perfect reliability. |
| [Google SRE Book: Postmortem Culture](https://sre.google/sre-book/postmortem-culture/) | Opening definition; `Google's Postmortem Philosophy`; `Best Practice: No Postmortem Left Unreviewed` | 25 minutes | Document failures, review them and turn lessons into follow-up work. |

Both resources are free.

## Optional reading

Revisit only the exact Week 6 OWASP prompt-injection sections and Week 10 excessive-agency mitigations if the threat model or safety tests are incomplete. Do not start a new course.

## Skip for now

Skip cloud deployment, production data, live credentials, managed vector databases, paid model APIs, fine-tuning, agent frameworks, multi-agent design, remote MCP, autonomous remediation, polished frontend work and benchmark claims based on a single incident.

## Required capstone scope

The capstone is complete only if it includes:

1. One synthetic incident replay.
2. Local runbook retrieval with citations.
3. Structured output validation.
4. Three read-only MCP tools, with at most two exposed to the agent.
5. Bounded agent steps and terminal states.
6. Deterministic policy.
7. Hash-bound human approval.
8. Simulation only.
9. Audit events and local telemetry.
10. Versioned evaluation results.

## Target project structure

```text
projects/read-only-ops-copilot/
|-- README.md
|-- architecture.md
|-- threat-model.md
|-- pyproject.toml or requirements.txt
|-- corpus/
|-- fixtures/
|-- indexes/
|-- prompts/
|-- schemas/
|-- mcp_server/
|-- agent/
|-- policy/
|-- approval/
|-- simulation/
|-- observability/
|-- audit/
|-- evals/
|-- scripts/
|   |-- run_demo.py
|   |-- run_evals.py
|   +-- verify_capstone.py
+-- tests/
```

Keep generated, cache and secret files out of Git. Include `.env.example` only if the project has optional environment variables; never commit `.env`.

## Hands-on lab

### Step 1: Freeze the capstone contract

In the README, state:

```text
Problem: assist an operator in triaging a recorded incident.
Input: local sanitized fixtures and runbooks.
Output: cited structured analysis and simulated proposal.
User: DevOps/SRE learner or reviewer.
Non-goals: production access, autonomous remediation, root-cause guarantee.
```

Freeze versions:

```yaml
capstone_version: 0.1.0
dataset_version: week-12-v1
prompt_version: rag-analysis-v1
schema_version: 1.0.0
policy_version: week-10-v1
```

### Step 2: Create one reproducible entrypoint

Provide a command such as:

```bash
python projects/read-only-ops-copilot/scripts/run_demo.py \
  --incident incident-001 --planner recorded --mode shadow
```

The command must finish with a clear terminal state and print artifact paths:

```text
terminal=SIMULATION_COMPLETE
analysis=artifacts/incident-001/analysis.json
approval=artifacts/incident-001/approval.json
simulation=artifacts/incident-001/simulation.txt
audit=artifacts/incident-001/audit.jsonl
trace=artifacts/incident-001/trace.json
```

Provide a no-approval scenario that ends at `AWAITING_HUMAN_REVIEW`.

### Step 3: Consolidate at least 20 evaluation cases

The suite must include:

| Category | Minimum |
| --- | ---: |
| Normal structured analysis | 3 |
| Missing or conflicting evidence | 3 |
| RAG retrieval and citation | 4 |
| Prompt injection and fake secret | 3 |
| MCP invalid input and boundary | 3 |
| Agent loop termination | 2 |
| Policy, approval and proposal mutation | 2 |
| **Total** | **20** |

Run:

```bash
python projects/read-only-ops-copilot/scripts/run_evals.py \
  --dataset projects/read-only-ops-copilot/evals/datasets/week-12-v1.yaml
```

Hard gates:

- No unsafe or non-allowlisted tool executes.
- No invalid schema output is accepted.
- No unknown evidence citation is accepted.
- No prompt injection is followed.
- No production target is allowed.
- No changed proposal reuses old approval.
- No external mutation occurs.

### Step 4: Inject failures

Demonstrate at least five failures:

1. Missing runbook.
2. Malicious retrieved instruction.
3. Invalid MCP argument.
4. Planner repeats a tool call.
5. Approved proposal changes before simulation.

For each, show the terminal state, audit event and user-visible explanation.

### Step 5: Verify from a clean environment

Document:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -r projects/read-only-ops-copilot/requirements.txt
python projects/read-only-ops-copilot/scripts/verify_capstone.py
```

Pin or constrain dependencies. If an embedding model requires a one-time download, document it and provide recorded retrieval output for offline review.

Expected verification summary:

```text
unit_tests=pass
eval_cases=20
hard_gate_failures=0
unsafe_actions_executed=0
external_mutations=0
demo_fixture=pass
```

### Step 6: Complete architecture and threat model

Architecture must show:

- Trust boundaries.
- Data flow.
- Model boundary.
- MCP server and tool allowlist.
- Policy/approval/simulation separation.
- Audit and telemetry destinations.

Threat model must cover:

- Prompt injection.
- Malicious or stale runbooks.
- Sensitive-data leakage.
- Excessive agency.
- Approval replay or proposal mutation.
- Audit tampering limitation.
- Denial of service and unbounded loops.

For each threat, record current mitigation and remaining risk.

### Step 7: Prepare a five-minute demo

Use this order:

1. Problem and non-goals — 30 seconds.
2. Architecture and trust boundary — 60 seconds.
3. Happy-path incident replay — 90 seconds.
4. One prompt-injection or approval-mutation failure — 60 seconds.
5. Evaluation report and known gaps — 60 seconds.

Do not spend the demo scrolling through code.

## Common mistakes

| Mistake | Fix |
| --- | --- |
| Adding another framework in capstone week | Integrate and verify existing components. |
| README claims production-ready | State that it is local, synthetic and shadow-mode. |
| Only happy-path demo exists | Show at least one blocked adversarial case. |
| Test count is reported without categories | Publish dataset version, categories and failures. |
| Human approval is clicked but not auditable | Show the exact proposal hash and approval record. |
| Architecture hides trust boundaries | Mark model output and retrieved content as untrusted. |

## Artifacts to commit

- `projects/read-only-ops-copilot/README.md`
- `projects/read-only-ops-copilot/architecture.md`
- `projects/read-only-ops-copilot/threat-model.md`
- Dependency manifest and clean-environment instructions
- `projects/read-only-ops-copilot/scripts/run_demo.py`
- `projects/read-only-ops-copilot/scripts/verify_capstone.py`
- `projects/read-only-ops-copilot/evals/datasets/week-12-v1.yaml`
- `projects/read-only-ops-copilot/evals/reports/week-12-capstone.md`
- Sanitized happy-path and blocked-failure artifacts
- `notes/week-12-retrospective.md`

## Evaluation

| Check | Required target |
| --- | ---: |
| Versioned evaluation cases | At least 20 |
| Hard safety gate failures | 0 |
| Unknown evidence citations accepted | 0 |
| Prompt injections followed | 0 |
| Invalid or unauthorized tool calls executed | 0 |
| Proposal mutation using stale approval | 0 |
| External mutations | 0 |
| Clean-environment verification | Pass or documented reproducible blocker |

Report quality metrics and human rubric scores separately from hard gates. Do not hide failed non-safety cases; make them backlog items.

## Safety boundary

- Local, synthetic and sanitized input only.
- Recorded/local planner and model path must be available.
- No paid API is required.
- No production credentials, raw production data or external write integrations.
- Model output and retrieved content are untrusted.
- Authorization, policy, approval and simulation remain outside the model.
- The final capstone is a learning portfolio, not a production remediation system.

## Definition of Done

- [ ] One command runs the documented shadow-mode workflow.
- [ ] A no-approval run stops safely.
- [ ] At least 20 versioned evaluation cases execute.
- [ ] All hard safety gates pass.
- [ ] Five injected failures have expected terminal states.
- [ ] Architecture and threat model match the implementation.
- [ ] A clean-environment setup is documented and verified.
- [ ] README states problem, scope, non-goals, commands, metrics and known gaps.
- [ ] The five-minute demo includes both success and blocked failure.
- [ ] No paid API, production credential or external mutation is required.

## Knowledge check

1. Why does a passing 20-case suite not prove production readiness?
2. Which artifact proves a changed proposal cannot reuse approval?
3. What is the strongest portfolio evidence in this project?
4. What should become the first backlog item after the capstone?

Expected answers:

1. The dataset is small, synthetic and cannot cover production distribution or all threats.
2. The hash-bound approval test and audit sequence.
3. Reproducible code, versioned evals, visible safety failures and honest boundaries together.
4. The highest-risk measured gap, not whichever new framework looks interesting.

## What you have achieved

You now have a coherent foundation portfolio for the transition from DevOps to LLMOps and AgentOps: a locally reproducible, observable and evaluated AIOps copilot with read-only tools, bounded agency, deterministic controls, human approval, simulation and audit.

## Reflection

After showing this capstone to an SRE team, what evidence would they still need before trusting it with sanitized shadow traffic, and what evidence would be required much later before any real action capability?
