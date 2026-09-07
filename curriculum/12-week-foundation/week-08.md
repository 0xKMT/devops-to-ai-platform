# Week 8: Design Read-Only MCP Tools

## Outcome

Define narrow MCP resources and tools that expose only sanitized, read-only operational data.

## Timebox

- Reading: 2 hours
- Contract design: 3-4 hours
- Security review: 1-2 hours

## Learn

- MCP hosts, clients and servers.
- Resources, prompts and tools.
- Tool discovery, input schemas and results.
- Capability boundaries and least privilege.
- Why generic execution tools create excessive agency.

## References

- [MCP: Introduction](https://modelcontextprotocol.io/docs/getting-started/intro)
- [MCP: Tools specification](https://modelcontextprotocol.io/specification/2026-07-28/server/tools)
- [VersusControl: MCP for DevOps](https://github.com/VersusControl/devops-ai-guidelines/tree/main/02-mcp-for-devops)

## Hands-on exercise

Design three read-only tools:

- `search_fixture_logs`
- `get_runbook_section`
- `explain_saved_tf_plan`

For each tool, define purpose, JSON input, output, errors, data source, maximum result size, audit event and forbidden behavior.

## Commit evidence

- MCP architecture note.
- Three tool contracts.
- Permission matrix.
- Positive and negative contract tests on paper or as fixtures.
- Weekly progress entry.

## Done checklist

- [ ] No shell, arbitrary SQL, arbitrary URL fetch or mutation tool exists.
- [ ] Inputs and outputs are bounded by schemas and size limits.
- [ ] Data sources are local and sanitized.
- [ ] Tool denial and failure are auditable.
- [ ] Tool descriptions do not imply permissions they do not have.

## Reflection

Could each tool be replaced by a smaller capability with a lower blast radius?
