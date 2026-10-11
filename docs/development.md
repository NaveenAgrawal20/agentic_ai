# Development across two laptops

Git is the shared source of truth for code, instructions, lockfiles, and sanitized learning notes. Codex sessions, credentials, virtual environments, and running processes are local to each machine.

## Starting and finishing a session
1. Use the same supported Codex CLI release on both laptops. Authenticate locally; never copy `.codex` authentication files into this repository. Review this repository's `.codex/config.toml` and mark the checkout trusted through your local Codex setup. Project configuration applies to Codex CLI where supported; cloud and organization policies may override it.
2. Run `git status --short`. With a clean worktree, pull the branch using `git pull --ff-only`. If changes exist, preserve and resolve them before pulling; avoid automatic resets or force pushes.
3. Read `AGENTS.md`, the project plan, and the latest learning log. Tell Codex which milestone and concept you want to learn.
4. Work on a small branch. Validate the change and update the learning log with a reproducible handoff.
5. Review `git diff` and `git status --short` for secrets and generated data. Commit and push when you choose to sync. On the other laptop, fetch and check out that branch before continuing. Avoid editing the same branch on both machines concurrently.

Suggested prompt: “Read AGENTS.md and the latest learning log. Help me complete milestone 1 in small steps, explain the design choices, and verify each step offline before proposing a live OpenRouter call.”

## Dependencies and credentials
Use separate virtual environments for the notebook's older pinned stack and the Python 3.13 MCP example. Recreate environments locally from their manifests and lockfiles; do not sync environments. Laptop setup and application validation are still pending.

Store secrets in a local ignored `.env` or your platform's secure environment settings. Commit only sanitized examples when needed. The existing notebook expects `NEMOTRON_4_340B_INSTRUCT_KEY`; a new application can standardize on `OPENROUTER_API_KEY` through an explicit implementation change. OpenRouter uses `https://openrouter.ai/api/v1`. Select a currently available model explicitly and set limits; free model availability and rate limits can change. Never assume provider-side data retention or routing guarantees—check the selected provider's settings before submitting sensitive data.

Codex authentication is separate from the application's OpenRouter key. This project configuration does not route Codex itself through OpenRouter. Do not add absolute laptop paths, authentication tokens, or automatic MCP startup to shared configuration.

## Three kinds of records
- **Learning log:** `docs/learning-log.md`, committed and sanitized. Record decisions, concepts learned, verification, and the next step so either laptop can resume.
- **Application logs:** structured JSON to stdout in services; local development logs may live in ignored `logs/`. These are operational events, not a transcript or a memory database.
- **Eval reports:** synthetic fixtures and sanitized summary reports may be committed intentionally. Raw traces and provider responses belong in ignored `artifacts/` with explicit retention.

## Application logging contract (to implement with the service)
Use Python's standard logging behind a shared configuration function; add JSON formatting and request context when FastAPI is introduced. Avoid scattered `print` calls and duplicate handlers.

Allowlisted fields: UTC timestamp, severity, event name, service/version, request/trace/run ID, agent or tool name, outcome, duration_ms, configured model ID, token counts, retry count, and estimated cost plus currency when measurable. Mark cost estimates and unknown values honestly. Avoid user identifiers by default; do not use correlation IDs as authorization.

Example using synthetic data:

```json
{"timestamp":"2026-10-11T00:00:00Z","level":"INFO","event":"tool.completed","run_id":"demo-run","tool":"search_docs","outcome":"success","duration_ms":42}
```

- INFO: lifecycle and outcomes. WARNING: retries and degraded behavior. ERROR: operation failures. DEBUG: sanitized development diagnostics, disabled by default in production.
- Never log keys, headers, cookies, connection strings, raw bodies, prompts, completions, retrieved documents, embeddings, or personal data by default. Provider exceptions may include sensitive request details; log a safe error category and status, not arbitrary exception text.
- Redact before emitting, using an allowlist rather than relying solely on regex. Bound string sizes and sanitize control characters. Test redaction with synthetic keys and adversarial tool outputs.
- Propagate correlation IDs across API, orchestration, tools, and evals. Accept external IDs only after validation; generate one otherwise.
- Keep liveness/readiness signals distinct from successful agent execution. Include failure outcomes and latency; never turn errors into successful metrics.
- For local files, rotate by size and set a retention limit (initial target: seven days). In deployed systems, configure access control, retention, and deletion in the log backend. Document any temporary opt-in diagnostic capture and delete it afterward.
- For evals, record dataset version, code revision, model/provider configuration, prompt version, judge configuration, failures, latency, and cost. Do not promise deterministic LLM results from a seed.

## Validation as the project grows
Choose checks that exercise changed behavior. The current repository has no established test suite or FastAPI service. Introduce pytest, linting, typing, and CI with the relevant milestone rather than claiming they already exist. Keep live provider tests opt-in and bounded; use fake providers for ordinary CI. Container readiness must eventually include a functional request, not just an open port.
