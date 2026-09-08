# Week 10: Add Policy, Approval and Audit Controls

## What you are building

This week you will place a deterministic control plane between agent proposals and any simulated action.

```text
untrusted model proposal
          |
          v
schema validation
          |
          v
deterministic policy decision
          |
     +----+-------------------+
     |                        |
 allow_read              require approval / deny
     |                        |
 read-only tool        human reviews exact proposal hash
                              |
                              v
                       simulation only
                              |
                              v
                         audit trail
```

## Outcome

By the end of this week, you can:

- Express operational decisions as deterministic policy outcomes.
- Keep authorization and approval outside the LLM.
- Bind human approval to an immutable proposal hash.
- Simulate a proposed action without executing it.
- Record append-only audit events for allow, deny, approval and simulation.
- Prove that changing a proposal invalidates its approval.

## Dependency

You need:

- Week 9 bounded agent runtime and terminal states.
- Week 8 MCP tool contracts.
- Week 7 safe telemetry fields.
- Week 3 schema validator.

## Time plan

| Session | Activity | Time |
| --- | --- | ---: |
| 1 | Excessive agency and control-plane reading | 1.25 hours |
| 2 | Define proposal and policy contracts | 1.5 hours |
| 3 | Implement policy and approval binding | 2 hours |
| 4 | Add simulator, audit trail and tests | 2 hours |
| 5 | Review and reflect | 0.75 hour |
| **Total** |  | **7.5 hours** |

## Mental model

The LLM may recommend an action, but it cannot authorize itself.

```text
recommendation != authorization
authorization != approval
approval != execution
simulation != production mutation
```

Use four explicit policy outcomes:

| Outcome | Meaning |
| --- | --- |
| `allow_read` | A known read-only tool may run. |
| `deny` | The request is prohibited or malformed. |
| `require_human_approval` | A bounded simulation proposal needs explicit review. |
| `simulate_only` | Produce a preview; no external system may change. |

## Required reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [OWASP LLM06:2025 Excessive Agency](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/) | Root causes `excessive functionality`, `excessive permissions`, `excessive autonomy`; `Prevention and Mitigation Strategies` items 4-8 | 40 minutes | Ground least privilege, human approval and complete mediation in concrete risks. |
| [MCP specification: Tools](https://modelcontextprotocol.io/specification/2026-07-28/server/tools) | `User Interaction Model` and `Security Considerations` | 20 minutes | Reinforce confirmation, input validation, access control and result validation. |

Both resources are free.

## Optional reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [Open Policy Agent: Policy Language](https://www.openpolicyagent.org/docs/policy-language) | `The Basics`; `Rules`; `Complete Definitions` | 30 minutes | See how policy can be separated from application code. Do not adopt OPA unless it helps your lab. |

## Skip for now

Skip production IAM integration, OAuth, policy distribution, signed approvals, OPA deployment, admission controllers, live change APIs, automated remediation and production credentials.

## Proposal contract

A proposed action must be data, not executable text:

```json
{
  "proposal_id": "prop-demo-001",
  "request_id": "req-demo-010",
  "actor": "learner",
  "action": "restart_workload",
  "target": {
    "environment": "sandbox",
    "resource_id": "demo-api"
  },
  "parameters": {
    "replicas": 2
  },
  "evidence_ids": [
    "log-17",
    "rb-api-high-latency#safe-next-step"
  ],
  "policy_version": "week-10-v1"
}
```

Canonicalize the validated proposal and compute a SHA-256 hash. Approval refers to the hash, not only `proposal_id`.

## Deterministic policy table

Start with rules such as:

| Condition | Decision |
| --- | --- |
| Known read-only tool, allowlisted fixture | `allow_read` |
| Unknown tool or malformed proposal | `deny` |
| Production target | `deny` |
| Secret, credential or raw customer-data request | `deny` |
| Write-like action against local sandbox fixture | `require_human_approval` |
| Approved exact proposal hash | `simulate_only` |
| Proposal changed after approval | `deny` |

Rule order and conflict behavior must be documented.

## Target project structure

```text
projects/read-only-ops-copilot/
|-- policy/
|   |-- policy-v1.yaml
|   |-- evaluator.py
|   +-- proposal-schema.json
|-- approval/
|   +-- approval_store.py
|-- simulation/
|   +-- simulate_action.py
|-- audit/
|   +-- events.jsonl
|-- tests/
|   +-- test_agent_controls.py
+-- docs/
    +-- agent-control-plane.md
```

## Hands-on lab

### Step 1: Validate and canonicalize proposals

The proposal schema must reject:

- Unknown fields.
- Missing actor, action, target or evidence.
- Unbounded free-form command fields.
- Production environment.
- URL, shell or arbitrary path parameters.

Hash only after validation and deterministic key ordering.

### Step 2: Implement policy as deterministic code or data

The evaluator input is the validated proposal plus trusted runtime context. It returns:

```json
{
  "decision": "require_human_approval",
  "policy_version": "week-10-v1",
  "reason_code": "SANDBOX_WRITE_SIMULATION",
  "proposal_hash": "sha256:..."
}
```

Never parse `approved=true` from model output as trusted context.

### Step 3: Build a human approval record

Use a local CLI:

```bash
python projects/read-only-ops-copilot/approval/approval_store.py \
  approve --proposal projects/read-only-ops-copilot/fixtures/proposals/sandbox-restart.json \
  --approver learner
```

Expected record:

```json
{
  "proposal_hash": "sha256:...",
  "approver": "learner",
  "decision": "approved_for_simulation",
  "policy_version": "week-10-v1",
  "expires_at": "..."
}
```

This is a learning implementation, not production authentication.

### Step 4: Simulate, never execute

The simulator may produce:

```text
SIMULATION ONLY
Would request restart of sandbox/demo-api
No Kubernetes client initialized
No external request sent
```

Do not import cloud or Kubernetes clients. Do not invoke shell commands.

### Step 5: Append audit events

Write one JSON line per event:

```text
proposal.received
proposal.validated
policy.decided
approval.recorded
simulation.completed
run.terminated
```

Each line contains timestamp, request ID, proposal hash, event, actor or component, decision and reason code. Redact content fields.

### Step 6: Test at least ten cases

Include:

1. Allowlisted read.
2. Unknown tool.
3. Malformed proposal.
4. Production target.
5. Credential request.
6. Sandbox write without approval.
7. Sandbox write with exact-hash approval.
8. Proposal changed after approval.
9. Expired approval.
10. Duplicate simulation request.

Expected summary:

```text
PASS allowlisted-read -> allow_read
PASS production-target -> deny
PASS sandbox-no-approval -> require_human_approval
PASS approved-exact-hash -> simulate_only
PASS modified-after-approval -> deny

External mutations: 0
Audit-complete cases: 10/10
```

## Common mistakes

| Mistake | Fix |
| --- | --- |
| Approval refers only to a proposal ID | Bind it to a canonical proposal hash and policy version. |
| Policy is written only in the prompt | Enforce it in deterministic code outside the model. |
| Simulation imports a real cluster client | Emit a pure local preview with no external client. |
| Denials have no reason code | Return stable machine-testable reason codes. |
| Audit stores the entire proposal | Store hashes and safe metadata; keep sanitized fixtures separately. |

## Artifacts to commit

- `projects/read-only-ops-copilot/policy/policy-v1.yaml`
- `projects/read-only-ops-copilot/policy/evaluator.py`
- `projects/read-only-ops-copilot/policy/proposal-schema.json`
- `projects/read-only-ops-copilot/approval/approval_store.py`
- `projects/read-only-ops-copilot/simulation/simulate_action.py`
- `projects/read-only-ops-copilot/tests/test_agent_controls.py`
- `projects/read-only-ops-copilot/docs/agent-control-plane.md`
- A sanitized sample of `projects/read-only-ops-copilot/audit/events.jsonl`
- `notes/week-10-reflection.md`

## Evaluation

| Check | Required target |
| --- | ---: |
| Policy cases with expected decision | 10/10 |
| Production-target proposals allowed | 0 |
| Mutated proposals using old approval | 0 |
| Simulations without exact approval | 0 |
| External mutations | 0 |
| Cases with complete audit sequence | 10/10 |
| Secret-like values in audit | 0 |

## Safety boundary

- No production target can pass policy.
- No real execution client, credential or external request exists.
- All write-like actions end at `simulate_only`.
- Approval is explicit, local, hash-bound and short-lived.
- The model cannot write policy, trusted context or approval records.
- Audit records contain safe metadata only.

## Definition of Done

- [ ] Proposal validation rejects unknown and dangerous fields.
- [ ] Policy produces only the four documented outcomes.
- [ ] Approval is bound to proposal hash and policy version.
- [ ] Editing a proposal invalidates its prior approval.
- [ ] The simulator has no external execution dependency.
- [ ] Ten policy and approval cases pass.
- [ ] Every case has a complete audit sequence.
- [ ] External mutation count is zero.

## Knowledge check

1. Why is human approval alone insufficient if it is not hash-bound?
2. Can the model provide trusted authorization context?
3. What is complete mediation in this lab?
4. Why distinguish `require_human_approval` from `simulate_only`?

Expected answers:

1. The action could change after a human reviewed it.
2. No. Model output is untrusted.
3. Every proposed capability call is checked by policy before dispatch.
4. One requests a decision; the other permits only a deterministic local preview.

## What you have achieved

You now have the foundation of an AgentOps control plane: typed proposals, deterministic policy, exact approval binding, simulation and an auditable terminal decision.

## Reflection

Which component can prove what the human approved, and which separate component can prove what the system actually simulated?
