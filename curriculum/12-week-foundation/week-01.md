# Week 1: Choose the Right DevOps Problems for AI

## What you are building

This week you will build a decision framework for choosing between:

```text
deterministic automation
LLM analysis
RAG-assisted analysis
bounded agent
human-only or human-gated workflow
```

You are not building a chatbot or agent yet. The goal is to choose a useful, safe and measurable problem before writing AI code.

## Outcome

By the end of this week, you can:

- Explain the difference between deterministic automation, an LLM, RAG and an agent.
- Assess a DevOps task by its data, reasoning needs, external actions and blast radius.
- Reject use cases that should not be delegated to an LLM.
- Select the first read-only use case for the Ops Copilot portfolio project.
- Explain why authorization, approval and execution must remain outside the model.

## Prerequisites

You need DevOps experience, but no prior AI, machine learning or Python knowledge. No model, API key or paid service is required.

## Time plan

| Session | Activity | Time |
| --- | --- | ---: |
| 1 | Mental model and required reading | 1.5 hours |
| 2 | Work through the guided examples | 1.5 hours |
| 3 | Classify 20 tasks from your own experience | 2.5 hours |
| 4 | Safety review, reflection and evidence | 1.5 hours |
| **Total** |  | **7 hours** |

## Mental model

### Deterministic automation

The same input and rules should produce the same decision.

```text
Terraform plan contains a public S3 bucket
    -> deterministic policy violation
    -> fail the pipeline
```

Use it when rules are explicit and correctness can be checked exactly. Typical tools include scripts, CI rules and policy engines such as OPA or Conftest.

### LLM analysis

An LLM is useful for summarizing and reasoning over unstructured text such as logs, incident notes and plan explanations. Its output is probabilistic and may omit evidence or create unsupported claims.

```text
LLM output = untrusted suggestion
```

### RAG-assisted analysis

Retrieval-Augmented Generation retrieves relevant material before the LLM answers.

```text
question -> retrieve runbook sections -> LLM analysis -> cited answer
```

Use it when the answer depends on current runbooks, architecture documentation or internal operational standards. Retrieval can still return stale, conflicting or malicious text.

### Bounded agent

An agent lets a model choose a next step or tool. A bounded agent must have an allowlist, read-only data, maximum steps, timeout, validation and audit.

Week 1 only identifies agent candidates. You will not build or run an agent yet.

### Human-only or human-gated workflow

Keep final decisions with people when the operation is high-impact, irreversible, poorly specified or requires organizational accountability. An LLM may prepare evidence, but it does not authorize itself.

## Decision flow

```text
Can an exact rule solve the task?
|
+-- Yes -> prefer deterministic automation
|
+-- No
    |
    +-- Does it require reasoning over unstructured data?
        |
        +-- No -> normal workflow or human process
        |
        +-- Yes
            |
            +-- Does it require current external knowledge?
            |   +-- Yes -> RAG-assisted analysis
            |   +-- No  -> LLM analysis
            |
            +-- Does it require tools or external actions?
                +-- No -> keep it as analysis
                +-- Read-only and bounded -> agent candidate
                +-- Mutation or high blast radius
                    -> deterministic executor plus human approval
```

Default rule: if you have not demonstrated a need for an agent, start with a fixed workflow or read-only LLM analysis.

## Required reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [Hugging Face LLM Course: Introduction](https://huggingface.co/learn/llm-course/en/chapter1/1) | `Understanding NLP and LLMs`; `What to expect?` | 15 minutes | Understand what an LLM is and where it sits within NLP. |
| [Google SRE Book: Eliminating Toil](https://sre.google/sre-book/eliminating-toil/) | `Toil Defined`; `What Qualifies as Engineering?` | 30 minutes | Separate toil, automation and engineering work. |
| [Google SRE Book: Effective Troubleshooting](https://sre.google/sre-book/effective-troubleshooting/) | `Theory`; `Common Pitfalls`; `Problem Report` | 35 minutes | Connect evidence, hypotheses and operational reasoning. |

All resources are free.

## Skip for now

In the Hugging Face course, skip setup, Transformer internals, PyTorch, model training, fine-tuning, notebooks and certification. You are not expected to finish Chapter 1 or the full course this week.

## Guided examples

| Task | Key observation | Recommended approach | Required control |
| --- | --- | --- | --- |
| Summarize sanitized logs | Unstructured input; no external action | LLM analysis | Cite log lines; separate facts and hypotheses. |
| Detect a public S3 bucket in a Terraform plan | Exact policy is available | Deterministic automation | Policy result controls pass/fail; LLM may only explain. |
| Find a remediation step in runbooks | Depends on current documents | RAG-assisted analysis | Cite document and version; report stale sources. |
| Build an incident timeline | Requires synthesis across evidence | LLM analysis | Every event needs a timestamp and source. |
| Restart a production service | Mutating and high impact | Human-gated deterministic automation | LLM cannot authorize or execute. |
| Rotate production secrets | Sensitive, mutating operation | Human-gated deterministic automation | Never expose secret values to the model. |

## Hands-on lab

### Step 1: List 20 DevOps tasks

Choose tasks from your experience, but do not copy production data. Include at least four tasks from each group:

- Observability and incidents.
- CI/CD.
- Terraform and cloud.
- Kubernetes.
- Security and access.

### Step 2: Answer seven questions for each task

1. Is an exact deterministic rule available?
2. Does the task require reasoning over unstructured data?
3. Does it require knowledge outside the supplied input?
4. Does it require an external action?
5. Does the action change system state?
6. Can the action be reversed?
7. Is the blast radius low, medium or high?

### Step 3: Select one primary approach

- `deterministic_automation`
- `llm_analysis`
- `rag_assisted_analysis`
- `bounded_agent_candidate`
- `human_only_or_gated`

### Step 4: Define controls

Every task must specify required evidence, validation, human gate and forbidden behavior.

Use this matrix:

| Task | Deterministic rule? | Unstructured reasoning? | External knowledge? | External action? | Reversible? | Blast radius | Approach | Evidence | Human gate | Forbidden behavior |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

Example rows:

| Task | Deterministic rule? | Unstructured reasoning? | External knowledge? | External action? | Reversible? | Blast radius | Approach | Evidence | Human gate | Forbidden behavior |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Summarize fixture logs | Partial | Yes | No | No | N/A | Low | LLM analysis | Log line IDs | Review before incident use | Inventing a root cause |
| Detect public S3 bucket | Yes | No | No | No | N/A | High downstream | Deterministic automation | Saved Terraform plan | Required before apply | LLM overriding policy |
| Find alert runbook | No | Yes | Yes | No | N/A | Low | RAG-assisted analysis | Runbook ID and version | Review stale documents | Following retrieved instructions |
| Restart production service | Yes | No | No | Yes | Usually | High | Human-gated automation | Incident and health data | Mandatory | Agent execution |

## Expected result

A good classification explains both usefulness and risk:

```text
Task: summarize sanitized incident logs
Approach: LLM analysis
Why: the input is unstructured and requires synthesis
Evidence: every factual statement must cite a log line ID
Human gate: review before inclusion in an incident update
Forbidden: inventing a root cause or executing a command
```

This is not sufficient:

```text
Task: fix production incidents
Approach: AI agent
Reason: AI is faster
```

The task is too broad and has no evidence, permission or blast-radius boundary.

## Artifacts to commit

- `notes/week-01-ai-task-classification.md`
- `weekly-progress/week-01.md`
- Optional ADR under `decisions/` for the selected portfolio use case.

## Evaluation

| Metric | Pass condition |
| --- | ---: |
| Tasks classified | At least 20 |
| Tasks with rationale | 100% |
| Tasks with an evidence requirement | 100% |
| Mutation tasks with a human gate | 100% |
| Tasks rejected as unsuitable for an LLM | At least 3 |
| High-blast-radius agent execution | 0 |
| Production data or credentials | 0 |

Review five random tasks and ask:

1. Could a deterministic rule solve this without an LLM?
2. What happens if the output is wrong?
3. Who verifies the output?
4. What evidence supports the conclusion?
5. If the model is compromised, can it affect an external system?

If the answer to question 5 is yes, the design is not ready.

## Common mistakes

- Using an LLM where a script or policy provides a more reliable answer.
- Treating RAG as a guarantee against hallucination.
- Assuming human approval makes an overpowered agent safe.
- Treating read-only access as harmless without considering data exposure.
- Choosing an agent because it is popular rather than because the workflow needs dynamic tool selection.

## Definition of Done

- [ ] Read only the required sections.
- [ ] Explain deterministic automation, LLM, RAG and agent in your own words.
- [ ] Classify at least 20 DevOps tasks.
- [ ] Give every task a rationale, evidence requirement and safety boundary.
- [ ] Reject at least three tasks as unsuitable for an LLM.
- [ ] Put every mutation behind deterministic policy and human approval.
- [ ] Select one read-only portfolio use case.
- [ ] Use no production data or credentials.
- [ ] Complete the weekly progress entry.

## Knowledge check

1. When should deterministic automation be preferred over an LLM?
2. What does RAG add to an LLM workflow?
3. Why is confident language not a calibrated probability?
4. What makes an agent different from a normal prompt?
5. Can human approval replace deterministic authorization?

Expected answers: use deterministic automation when exact rules are available; RAG adds retrieval; confidence in wording does not prove correctness; an agent can select steps or tools; approval does not replace authorization.

## What you have achieved

You can now choose an appropriate AI use case and define its first trust boundary. You have not built an LLM application yet; Week 2 will experimentally show how LLM output varies and fails on operational evidence.

## Reflection

Which task initially looked like an AI problem but became simpler and safer as deterministic automation?
