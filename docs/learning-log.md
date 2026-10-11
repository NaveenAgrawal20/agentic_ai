# Learning and handoff log

Keep entries concise and free of credentials, personal data, raw provider output, and chat transcripts. Use one entry per meaningful milestone. Record actual outcomes, including skipped or blocked checks.

## 2026-10-11 — Repository guidance established
- Goal: learn agent engineering through an academic assistance service using OpenRouter, with portable handoffs across two laptops.
- Added: root agent instructions, shared Codex defaults, development/logging practices, and an incremental project plan.
- Existing code: LangGraph notebook and Python 3.13 cryptocurrency MCP example. No new application features implemented.
- Environment work: initial cloud dependency preparation started before the scope changed; no active setup installer was found after the pause. Local environment files are outside the checkout. Application startup and live integrations have not been validated.
- Verification: Python `tomllib` parsed the Codex configuration and checked its expected defaults; `git diff --check` passed; `git check-ignore` confirmed local secrets/logs/artifacts are ignored and shared configuration is not. Codex CLI 0.159.2 rejected `--strict-config features list` because that subcommand does not support strict configuration validation; project configuration activation on each laptop remains unverified. No application tests or provider calls ran for this milestone.
- Decision: keep notebook and MCP dependencies separate; use Git for durable handoffs and local credentials on each laptop.
- Next: start milestone 1 with a provider interface and offline tests, then an explicitly authorized bounded OpenRouter request.

## 2026-10-11 — Architecture records and operational flows
- Goal: maintain architecture decisions, a shared entrypoint, and operational pipelines; synchronize guidance through Git.
- Added: root `decisions.md` with ADR template and accepted/proposed choices, `entrypoint.md` with reading order and laptop handoff, and `flow.md` with tables and Mermaid request/registration/CI-CD diagrams.
- Updated: `AGENTS.md` requires maintaining these documents with relevant changes. Includes earlier guidance and shared Codex defaults in the same documentation delivery.
- Verification: Python checked local Markdown links, paired code fences, and TOML syntax; `git diff --check` passed. Mermaid rendering and application behavior were not tested; no runtime features were added.
- Decision: PostgreSQL and managed OIDC are proposals awaiting milestone-specific validation; documented delivery flows do not authorize deployment.
- Next: resume milestone 1 from `entrypoint.md`. Git delivery result is reported separately; laptop environment activation remains pending.

## Entry template
- Date and milestone:
- Goal and concept learned:
- Changes and design decisions:
- Verification commands and actual outcomes:
- Known issues or blockers:
- Next small step:
