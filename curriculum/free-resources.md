# Free Learning Resources

All resources below are free to read. Some vendor APIs mentioned in their documentation may require payment; the first 12 weeks should use local models, an existing chat product, or fake/recorded responses when an API is not free.

## Core sources

| ID | Resource | Use in this curriculum |
| --- | --- | --- |
| R1 | [Hugging Face LLM Course](https://huggingface.co/learn/llm-course/en/chapter1/1) | LLMs, transformers, inference, limitations and later advanced study |
| R2 | [Hugging Face Agents Course](https://huggingface.co/learn/agents-course/unit0/introduction) | Agent fundamentals, tools, frameworks, Agentic RAG and evaluation |
| R3 | [OpenAI Evals guide](https://developers.openai.com/api/docs/guides/evals) | Evaluation lifecycle, datasets, graders and regression thinking |
| R4 | [Hugging Face RAG Evaluation](https://huggingface.co/learn/cookbook/rag_evaluation) | RAG datasets, groundedness, relevance and retrieval experiments |
| R5 | [OpenTelemetry signals](https://opentelemetry.io/docs/concepts/signals/) | Traces, metrics, logs and context correlation |
| R6 | [Prometheus overview](https://prometheus.io/docs/introduction/overview/) | Time-series metrics, labels, PromQL and alerting foundations |
| R7 | [Model Context Protocol introduction](https://modelcontextprotocol.io/docs/getting-started/intro) | MCP architecture, servers, clients, resources, prompts and tools |
| R8 | [MCP tools specification](https://modelcontextprotocol.io/specification/2026-07-28/server/tools) | Tool schemas, discovery and invocation contracts |
| R9 | [Google SRE Books](https://sre.google/books/) | Toil, monitoring, troubleshooting, incident response and reliability |
| R10 | [OWASP GenAI Security Top 10](https://genai.owasp.org/initiatives/top-10-for-llm-and-genai/) | Prompt injection, output handling, excessive agency and consumption risks |
| R11 | [Google Cloud MLOps guide](https://docs.cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning) | CI/CD/CT, data validation, model validation and MLOps maturity |
| R12 | [VersusControl DevOps AI Guidelines](https://github.com/VersusControl/devops-ai-guidelines) | DevOps-oriented exercises for MCP, agents, monitoring, RAG and evaluation |

## How to use the sources

- Treat official specifications and project documentation as the authority for current interfaces.
- Treat R12 as a practical map, not the sole technical authority.
- Record the URL and access date in notes when behavior may change.
- Prefer one primary source and at most one supporting source per learning objective.
- Do not copy an example into production without reviewing permissions, failure modes and data exposure.

## Deferred material

These topics are intentionally outside the first 12 weeks:

- Training a foundation model.
- Advanced fine-tuning and distributed GPU infrastructure.
- Multi-agent architectures.
- Autonomous production remediation.
- Building a full feature store or continuous-training platform.
