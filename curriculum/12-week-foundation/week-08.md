# Week 8: Build a Read-only MCP Server

## What you are building

This week you will expose three narrow Ops Copilot capabilities through a runnable local MCP server:

- `search_fixture_logs`
- `get_runbook_section`
- `explain_saved_tf_plan`

The tools accept fixture IDs from an allowlist. They do not accept arbitrary paths, URLs, shell commands or cloud credentials.

```text
local MCP client
       |
       v
validated tool name + typed arguments
       |
       v
allowlisted fixture lookup
       |
       v
bounded structured result
```

## Outcome

By the end of this week, you can:

- Explain host, client, server, tools, resources and prompts in MCP.
- Build and run an MCP server over local stdio.
- Derive constrained input schemas from Python types.
- Return bounded, structured, sanitized tool results.
- Test valid, invalid and unauthorized fixture requests.
- Explain why read-only describes both implementation and underlying identity.

## Dependency

You need:

- Week 3 schema validation concepts.
- Week 5 runbook chunks.
- Week 6 adversarial fixtures.
- Week 7 safe telemetry fields.

## Time plan

| Session | Activity | Time |
| --- | --- | ---: |
| 1 | MCP concepts, tools and security reading | 1.5 hours |
| 2 | Define tool contracts and fixture registry | 1.5 hours |
| 3 | Implement the local MCP server | 2 hours |
| 4 | Add client tests and failure cases | 1.5 hours |
| 5 | Document the security boundary | 1 hour |
| **Total** |  | **7.5 hours** |

## Mental model

MCP standardizes communication; it does not automatically make a tool safe.

```text
model chooses a tool
client sends tools/call
server validates arguments
server enforces authorization and bounds
server returns untrusted result
client validates before model use
```

The model never receives direct access to Python functions, files or credentials. It proposes a protocol call that deterministic code must mediate.

## Required reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [MCP: What is the Model Context Protocol?](https://modelcontextprotocol.io/docs/getting-started/intro) | Opening definition; `What can MCP enable?`; `Why does MCP matter?`; `Start building` | 20 minutes | Establish the host-client-server mental model. |
| [MCP specification: Tools](https://modelcontextprotocol.io/specification/2026-07-28/server/tools) | `User Interaction Model`; `Capabilities`; `Listing Tools`; `Calling Tools`; `Data Types`; `Error Handling`; `Security Considerations` | 45 minutes | Learn the actual tool contract and mandatory validation boundary. |
| [Official MCP Python SDK: First steps](https://github.com/modelcontextprotocol/python-sdk/blob/main/docs/get-started/first-steps.md) | `Host, client, and server`; `The three primitives`; `One server, all three` → `Try it` | 30 minutes | Build a minimal local server with the current official SDK. |
| [Official MCP Python SDK: Tools](https://github.com/modelcontextprotocol/python-sdk/blob/main/docs/servers/tools.md) | `Your first tool`; `The input schema`; `What the model gets back`; `Richer schemas with Field` | 30 minutes | Design typed, constrained inputs and structured results. |

All resources and local tooling are free. The optional MCP Inspector requires Node.js but no paid API.

## Optional reading

| Resource | Exact sections | Time | Why |
| --- | --- | ---: | --- |
| [Official MCP Python SDK: Testing](https://github.com/modelcontextprotocol/python-sdk/blob/main/docs/get-started/testing.md) | `An in-memory server`; `Test a tool`; `Test errors` | 20 minutes | Add protocol-level tests without a model or desktop client. |

## Skip for now

Skip remote HTTP transports, OAuth, production identity propagation, elicitation, sampling, roots, multi-server discovery, write tools and arbitrary resource browsing. Identity and authorization are revisited after the foundation.

## Tool contracts

### Tool 1: search_fixture_logs

```text
input:
  fixture_id: allowlisted string
  query: string, 1-100 characters
  limit: integer, 1-20, default 10
output:
  fixture_id
  matches[] with line_id and sanitized text
  truncated: boolean
```

### Tool 2: get_runbook_section

```text
input:
  document_id: allowlisted string
  section_id: allowlisted string
output:
  chunk_id
  title
  text
  version
  reviewed_at
  status
```

### Tool 3: explain_saved_tf_plan

```text
input:
  fixture_id: allowlisted string
output:
  create_count
  update_count
  delete_count
  replace_count
  risk_notes[]
```

This tool parses a sanitized, previously saved plan fixture. It never runs Terraform.

## Target project structure

```text
projects/read-only-ops-copilot/
|-- mcp_server/
|   |-- server.py
|   |-- fixture_registry.py
|   +-- models.py
|-- tests/
|   +-- test_mcp_tools.py
|-- fixtures/
|   |-- logs/
|   |-- runbooks/
|   +-- terraform-plans/
+-- docs/
    +-- mcp-security-boundary.md
```

## Hands-on lab

### Step 1: Create a fixture registry

Map opaque IDs to known local files inside deterministic code:

```python
FIXTURES = {
    "logs-api-timeout": "fixtures/logs/api-timeout.log",
    "plan-safe-update": "fixtures/terraform-plans/safe-update.txt",
}
```

The MCP input accepts `logs-api-timeout`, not `../../etc/passwd` or an absolute path. Resolve the path only after allowlist lookup and verify it remains under the fixture root.

### Step 2: Implement typed tools

Use the official Python SDK and type constraints. Every tool must have:

- A precise name and docstring.
- Typed and bounded arguments.
- A structured return type.
- A maximum result count and text length.
- Explicit not-found and invalid-input errors.
- No write operation.

### Step 3: Run the server locally

Use stdio as the core transport:

```bash
python -m projects.read-only-ops-copilot.mcp_server.server
```

If the project layout does not support module execution, document and use the equivalent direct path. The process should wait for MCP input and must not open a public network listener.

Optionally inspect it:

```bash
uv run mcp dev projects/read-only-ops-copilot/mcp_server/server.py
```

### Step 4: Test through an MCP client

Use the SDK's in-memory client or a subprocess stdio client. Test:

1. `tools/list` returns exactly the three tools.
2. Each valid call returns the documented shape.
3. Unknown fixture ID returns a controlled tool error.
4. `limit=999` is rejected before execution.
5. Path traversal input is rejected.
6. A write-like or unknown tool cannot be called.
7. Output truncation is explicit.
8. Fake-secret content is redacted.

Expected summary:

```text
PASS tools/list: 3 read-only tools
PASS search_fixture_logs valid
PASS search_fixture_logs rejects limit=999
PASS unknown fixture denied
PASS path traversal denied
PASS write tool unavailable
PASS output bounded and redacted
```

### Step 5: Add telemetry and audit events

Record safe fields:

```json
{
  "event": "mcp.tool.completed",
  "request_id": "req-demo-008",
  "tool_name": "search_fixture_logs",
  "fixture_id": "logs-api-timeout",
  "status": "ok",
  "result_count": 4,
  "truncated": false
}
```

Do not log query text or returned content by default.

### Step 6: Write the security boundary

Document:

- What each tool can read.
- Which fixture IDs exist.
- Which arguments are bounded.
- Which outputs are redacted or truncated.
- Which calls are impossible.
- Why stdio does not remove the need for input validation.

## Common mistakes

| Mistake | Fix |
| --- | --- |
| A tool accepts an arbitrary file path | Accept an opaque allowlisted fixture ID. |
| `read_only=true` exists only in the description | Remove write code and use a read-only data source or fixture. |
| Tool output can be unlimited | Bound records and characters; return `truncated`. |
| Tests call Python functions directly only | Add at least one protocol-level MCP client test. |
| A tool result is trusted because the server produced it | Validate and sanitize it before model use. |

## Artifacts to commit

- `projects/read-only-ops-copilot/mcp_server/server.py`
- `projects/read-only-ops-copilot/mcp_server/fixture_registry.py`
- `projects/read-only-ops-copilot/mcp_server/models.py`
- `projects/read-only-ops-copilot/tests/test_mcp_tools.py`
- `projects/read-only-ops-copilot/docs/mcp-security-boundary.md`
- `notes/week-08-reflection.md`

## Evaluation

| Check | Required target |
| --- | ---: |
| Tools exposed | Exactly 3 |
| Valid tool cases passing | 3/3 |
| Invalid input and unknown fixture cases rejected | 100% |
| Path or URL parameters exposed | 0 |
| Write-capable tools exposed | 0 |
| Fake-secret leakage | 0 |
| Unbounded result paths | 0 |

## Safety boundary

- Local stdio only.
- Fixture IDs only; no arbitrary paths or URLs.
- No cloud SDK, Kubernetes client, Terraform execution or shell subprocess.
- No production credentials or raw production data.
- Tool output is untrusted input to the model.
- MCP transport does not replace authorization, policy or approval.

## Definition of Done

- [ ] The server starts locally over stdio.
- [ ] `tools/list` returns exactly three documented tools.
- [ ] All input schemas have types and useful bounds.
- [ ] All results are structured, redacted and bounded.
- [ ] Unknown IDs, oversized limits and traversal inputs fail safely.
- [ ] Tests use an MCP client for at least one end-to-end call.
- [ ] Telemetry records metadata but not content.
- [ ] The security boundary lists every permitted data source.

## Knowledge check

1. Does MCP make a tool read-only?
2. Who chooses to call an MCP tool?
3. Why is a fixture ID safer than a file path?
4. Where must tool input and result validation occur?

Expected answers:

1. No. Safety comes from implementation, identity, permissions and policy.
2. Usually the model through a client, so the call must be mediated.
3. It limits access to a deterministic allowlist and blocks traversal.
4. The server validates inputs; the client/application also validates results before model use.

## What you have achieved

You now have a real, locally runnable MCP server whose capability surface is intentionally small. It exposes useful Ops evidence without granting filesystem, shell, cloud or production access.

## Reflection

If a tool is named `read_logs` but its implementation can read any path available to the process, is it read-only enough for an agent? Explain the missing controls.
