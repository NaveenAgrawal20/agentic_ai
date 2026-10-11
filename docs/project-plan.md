# Learning project: academic assistance agent service

Build on the existing academic assistant example: a service that answers questions about synthetic course material, proposes study plans, and produces notes with citations. This gives orchestration, tools, memory, and evals one concrete use case. Use synthetic users and course documents initially. Calendar writes and other external actions remain disabled until explicit authorization controls exist.

These are planned milestones, not implemented features. Start with Python, OpenRouter, LangGraph, and FastAPI. Learn MCP through one focused tool integration. Compare other frameworks later; do not add every framework to the core.

| Milestone | Deliverable | Evidence of completion |
| --- | --- | --- |
| 1. Provider foundation | Typed provider interface, configurable OpenRouter client, fake client, limits and sanitized logging | Offline response/error/timeout tests; opt-in bounded live request |
| 2. Backend | FastAPI request/response schemas, health/readiness, configuration and error handling | API integration tests; one complete synthetic request |
| 3. Orchestration | Explicit state, deterministic routing to planner/notes/advisor; bounded execution | Routing and termination tests; observable agent transitions |
| 4. Tools | Read-only course/calendar tools and one MCP integration | Schema validation, timeout, authorization and tool-failure tests |
| 5. Memory and RAG | Persisted state scoped by user, retrieval with citations and context budget | Retrieval eval, isolation/deletion tests, restart persistence |
| 6. Guardrails | Input/output validation, injection defenses, allowlisted tools, approval for writes | Adversarial dataset and unauthorized-action rejection tests |
| 7. Evals | Versioned synthetic dataset, scoring rubric, offline CI and optional live runs | Quality/safety baseline with latency/token/cost reports |
| 8. Operations | Docker, CI, telemetry, authenticated API, deployment design and runbook | Container functional smoke test; documented restore and failure handling |
| 9. Performance and portfolio | Concurrency/retry tuning, caching policy, architecture decisions and demo | Before/after measurements and documented tradeoffs |

Azure deployment can follow local container validation. Choose storage, vector database, cloud identity, and deployment targets when the relevant milestone needs them. Document data flows, secrets, tenant boundaries, budgets, and failure recovery before production use. Multi-agent collaboration should earn its complexity through measured quality improvements.

For each milestone, explain one design decision, ship one runnable increment, add meaningful validation, and write a short learning handoff. A working demo alone does not establish production readiness.
