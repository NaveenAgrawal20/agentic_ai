# Architecture decision records

Use stable sequential IDs. Statuses: Proposed, Accepted, Rejected, Deprecated, or Superseded. Accepted means a design decision is agreed for this learning project; it does not mean its implementation is complete. Preserve past records and link replacements rather than rewriting history. Review proposed decisions when their milestone begins.

## Index

| ID | Decision | Status | Implementation |
| --- | --- | --- | --- |
| ADR-001 | Python, OpenRouter, LangGraph, and FastAPI learning stack | Accepted | Examples exist; service pending |
| ADR-002 | Git-based handoffs and sanitized structured logging | Accepted | Guidance added; runtime logging pending |
| ADR-003 | PostgreSQL for durable application state | Proposed | Not implemented |
| ADR-004 | Managed OIDC authentication with OAuth authorization code and PKCE | Proposed | Not implemented |

## ADR-001: Learning stack and provider boundary
- **Date:** 2026-10-11
- **Status:** Accepted
- **Context:** Existing examples use Python and LangGraph; the project must teach backend APIs, tools, evals, and OpenRouter integration without adopting many frameworks at once.
- **Decision:** Use Python, a typed OpenRouter provider adapter, LangGraph for explicit orchestration, FastAPI for the future service, and MCP for one focused tool integration. Start with deterministic routing and fake providers. Keep existing notebook and MCP environments separate.
- **Alternatives:** Direct vendor APIs reduce intermediary dependencies but couple the project to each vendor. Multiple orchestration frameworks offer comparisons but increase learning and maintenance cost.
- **Consequences:** Reuses existing concepts and supports offline testing. OpenRouter adds provider variability, rate limits, and data-policy considerations; model IDs and budgets must be configured explicitly. No promise of interchangeable model behavior.
- **Validation:** Adapter response/error/timeout tests, routing and termination tests, and an opt-in bounded live request before claiming integration works.
- **Revisit when:** Provider requirements, measured quality, or framework limitations justify a change.

## ADR-002: Portable handoffs and logging boundaries
- **Date:** 2026-10-11
- **Status:** Accepted
- **Context:** Development spans two laptops; chat state and local environments are not portable. Operational diagnostics must avoid exposing credentials or user content.
- **Decision:** Commit code, lockfiles, ADRs, flow documentation, and sanitized learning notes. Keep credentials, environments, runtime logs, and raw traces local or in controlled storage. Follow the structured-event contract in [development.md](docs/development.md).
- **Alternatives:** Chat-only handoffs lose context across machines. Full prompt transcripts make debugging convenient but introduce privacy and retention risks.
- **Consequences:** Explicit handoffs require discipline; runtime events alone may not explain semantic failures. Use synthetic eval fixtures and temporary explicitly controlled diagnostics where necessary. Logs are not agent memory.
- **Validation:** Ignore-rule checks now; redaction, correlation, and error-sanitization tests when logging is implemented.
- **Revisit when:** Team collaboration or deployed retention requirements change.

## ADR-003: Durable state database
- **Date:** 2026-10-11
- **Status:** Proposed
- **Context:** Persisted user-scoped memory, profiles, and run metadata need consistency, isolation, deletion, and recovery. The current examples do not establish a production persistence layer.
- **Decision proposed:** PostgreSQL for durable application state, with migrations, least-privilege access, and user/tenant-scoped queries. Evaluate pgvector for RAG only when retrieval measurements justify it; do not assume it is required.
- **Alternatives:** SQLite is simpler for a local prototype but differs in concurrency and deployment behavior. A dedicated vector database may help specialized retrieval but adds an operational dependency. In-memory state cannot survive restart.
- **Consequences:** Adds a service, migration discipline, backup/restore, and connection-pool management. Database choice alone does not provide tenant isolation; authorization and isolation tests remain required.
- **Validation before acceptance:** Persistence across restart, cross-user access rejection, deletion semantics, representative query performance, and backup/restore exercise.
- **Revisit when:** Milestone 5 starts; select hosting and recovery objectives then.

## ADR-004: Registration and authentication
- **Date:** 2026-10-11
- **Status:** Proposed
- **Context:** A future UI needs user registration and authenticated API access. Custom password storage expands security scope beyond the learning project's core.
- **Decision proposed:** Delegate registration to a managed OpenID Connect identity provider. Use OAuth authorization code with PKCE for a public frontend; the API validates access tokens intended for its audience, issuer, signature, expiry, and required scopes. Bind a local profile to the trusted issuer/subject pair. Select provider and browser session strategy when a frontend exists.
- **Alternatives:** Local password authentication allows control but requires recovery, verification, hashing, and abuse prevention. API keys can serve machine clients but are not a user registration flow.
- **Consequences:** Adds identity-provider dependency and token lifecycle handling. Authentication is separate from authorization; tenant membership and tool permissions must be checked server-side. Never send access tokens or identity claims to the LLM by default.
- **Validation before acceptance:** Invalid/expired/wrong-audience token rejection, key rotation, logout/session behavior, duplicate profile prevention, and cross-user authorization tests. Confirm the selected provider's token format and verification method.
- **Revisit when:** Authenticated API and frontend milestones begin.

## ADR template

Copy this section for each new decision and add an index row.

### ADR-NNN: Short descriptive title
- **Date:** YYYY-MM-DD
- **Status:** Proposed
- **Owners:** Responsible maintainer(s)
- **Context:** Problem, constraints, relevant measurements, and requirements.
- **Decision:** Concrete choice, scope, and boundaries.
- **Alternatives:** Options considered and why they were not selected.
- **Consequences:** Benefits, costs, security/privacy effects, operational burden, and known risks.
- **Validation:** Evidence required; distinguish planned checks from completed results.
- **Revisit when:** Explicit triggers or review milestone.
- **References:** Related ADRs, documentation, issues, or measurements.
- **Supersedes / superseded by:** ADR ID when applicable.
