# Project entrypoint

Start here on either laptop. This is a documentation entrypoint, not an executable application entrypoint.

## Current state
The repository contains an academic-assistance notebook and a cryptocurrency MCP example. The FastAPI service, registration, persistent memory, and deployment pipeline are planned, not implemented. Cloud setup is paused; laptop setup and live integrations remain unverified.

## Reading order
1. [AGENTS.md](AGENTS.md): instructions for Codex and engineering habits.
2. [Learning log](docs/learning-log.md): latest completed work, verification, blockers, and next step.
3. [Project plan](docs/project-plan.md): milestones and completion evidence.
4. [Decisions](decisions.md): accepted and proposed architecture choices.
5. [Flows](flow.md): planned request, registration, and delivery pipelines.
6. [Development guide](docs/development.md): laptop handoffs, dependencies, credentials, and logging.

## Resume checklist
- Inspect `git status --short`; preserve local work before switching branches or pulling.
- Fetch the shared branch and use `git pull --ff-only` when the worktree is clean. Do not assume changes on another laptop have been pushed.
- Read the latest learning log and select one milestone. Recreate dependencies locally; virtual environments and Codex sessions are not shared through Git.
- Keep OpenRouter credentials local. The existing notebook reads `NEMOTRON_4_340B_INSTRUCT_KEY`; future standardization requires an explicit code change.
- Validate the selected change; record actual outcomes and the next step. Review the diff before committing and pushing.

## Documentation ownership
Maintain these documents alongside relevant changes:

| Change | Record |
| --- | --- |
| Durable system choice or trade-off | New ADR in `decisions.md` |
| Request lifecycle, registration, deployment, or recovery changes | `flow.md` |
| Startup or navigation changes | `entrypoint.md` |
| Milestone progress and verification | `docs/learning-log.md` |
| Logging or two-laptop workflow changes | `docs/development.md` |

Next implementation step: milestone 1, a typed provider interface with offline tests. This documentation change does not authorize paid calls or deployment.
