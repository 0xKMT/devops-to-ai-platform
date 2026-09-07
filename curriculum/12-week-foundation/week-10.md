# Week 10: AgentOps Authorization, Approval and Audit

## Outcome

Separate model suggestions from deterministic authorization, human approval and execution state.

## Timebox

- Reading: 2 hours
- Policy and threat model: 3-4 hours
- Adversarial review: 1-2 hours

## Learn

- Least privilege and scoped identity.
- Tool allowlists and policy enforcement.
- Human approval records.
- Idempotency, retry and replay.
- Prompt injection, improper output handling and excessive agency.

## References

- [OWASP: Top 10 for LLM and GenAI](https://genai.owasp.org/initiatives/top-10-for-llm-and-genai/)
- [OWASP: Excessive Agency](https://owasp.org/www-project-top-10-for-large-language-model-applications/2_0_vulns/LLM06_ExcessiveAgency.html)

## Hands-on exercise

Define a policy table with four outcomes:

- `allow_read`
- `deny`
- `require_human_approval`
- `simulate_only`

Replay at least ten requests, including attempts to restart, scale, delete, rotate, deploy, grant access and run arbitrary commands. The LLM may propose an action, but the policy engine decides its disposition.

## Commit evidence

- Threat model.
- Permission and policy table.
- Approval and audit event schemas.
- Ten adversarial authorization cases.
- Weekly progress entry.

## Done checklist

- [ ] The LLM cannot approve its own proposal.
- [ ] All mutation attempts are denied or simulated.
- [ ] Policy results are deterministic and testable.
- [ ] Retries have an idempotency strategy.
- [ ] Every decision records actor, input hash, policy version and outcome.

## Reflection

If the model were fully compromised, which controls would still prevent external impact?
