# Operational flows

These diagrams describe the target system, not running services. Registration and CI/CD are not implemented. Database and identity-provider choices are proposed in [decisions.md](decisions.md). Application logs follow [the development logging contract](docs/development.md).

## Agent request pipeline

```mermaid
flowchart TD
    U[Client request] --> A[Authenticate and authorize]
    A --> V[Validate input and assign run ID]
    V --> B[Check request and cost budgets]
    B --> R[Deterministic agent routing]
    R --> M[Load authorized memory and context]
    M --> P[Bounded planning and execution]
    P --> T{Tool needed?}
    T -->|Yes| G[Validate tool args and permissions]
    G --> W{Consequential write?}
    W -->|Yes| H[Obtain explicit approval]
    W -->|No| X[Execute tool with timeout]
    H -->|Approved| X
    H -->|Denied| F[Return safe failure]
    X --> O[Validate untrusted tool output]
    O --> L[OpenRouter provider adapter]
    T -->|No| L
    L --> C[Validate response and citations]
    C --> S[Persist permitted state]
    S --> E[Return response and sanitized metrics]
    A -->|Denied| F
    B -->|Exceeded| F
```

| Stage | Operational rule | Failure behavior |
| --- | --- | --- |
| API boundary | Validate schema, identity, authorization, and budgets | Return appropriate safe 4xx response |
| Context and memory | Scope by authenticated user/tenant; enforce context budget | Reject forbidden access; handle missing data explicitly |
| Orchestration | Bound iterations and concurrency; carry run ID | Stop on limit or cancellation |
| Tools | Allowlist tools; require approval for writes; bound outputs | Safe error; retry only when transient and side effects permit |
| Provider | Configured model, timeout, token/cost caps | Bounded backoff; never silently bypass policy |
| Output and persistence | Validate structure/citations; persist only permitted state | Report failure honestly; avoid partial-write ambiguity |

All stages emit sanitized outcomes and durations. The simplified diagram omits per-stage error edges; validation, authorization, and dependency failures must end safely rather than falling through to successful output.

## User registration and first access (proposed)

```mermaid
sequenceDiagram
    actor User
    participant UI as Frontend
    participant IdP as Managed identity provider
    participant API as FastAPI service
    participant DB as Application database
    User->>UI: Sign up or sign in
    UI->>IdP: Authorization request with PKCE and state
    IdP->>User: Registration and verification per policy
    User->>IdP: Complete identity checks
    IdP-->>UI: Authorization code
    UI->>IdP: Exchange code with PKCE verifier
    IdP-->>UI: Tokens
    UI->>API: Access token for this API
    API->>API: Validate token and authorization
    alt Invalid identity or access denied
        API-->>UI: Safe 401 or 403
    else Valid identity
        API->>DB: Idempotently provision permitted profile
        DB-->>API: Profile bound to issuer and subject
        API-->>UI: Minimal authorized profile
    end
```

| Step | Control | Acceptance evidence |
| --- | --- | --- |
| Identity enrollment | Provider owns password/recovery and verification policy | Verified registration and recovery paths |
| Browser redirect | PKCE, state validation, exact registered redirect URI; OIDC nonce when applicable | Reject replay and invalid callback state |
| API authentication | Verify signature or provider-supported token verification, issuer, audience, expiry, scopes | Reject malformed, expired, and wrong-audience tokens |
| Profile provisioning | Unique issuer/subject identity; default least privilege; no tenant inference from email alone | Duplicate requests do not create duplicate users |
| Subsequent access | Server-side resource and tool authorization | Cross-user access tests fail safely |

Do not log tokens, passwords, callback codes, or raw identity claims. Database outage must not yield a successful profile response. Rate limiting, account deletion, and revocation behavior must be specified before deployment.

## CI/CD delivery routine (planned)

```mermaid
flowchart TD
    C[Small branch and reviewed diff] --> PR[Pull request]
    PR --> Q[Locked install, lint, typing, offline tests and evals]
    Q --> D[Dependency and secret checks]
    D --> I[Build container and functional smoke test]
    I --> G{Required checks and review pass?}
    G -->|No| F[Fix on branch]
    F --> Q
    G -->|Yes| M[Merge approved change]
    M --> R[Publish immutable image digest]
    R --> S[Deploy staging with scoped identity]
    S --> V[Readiness and synthetic functional request]
    V --> A{Production approval?}
    A -->|Yes| P[Promote same tested image digest]
    A -->|No| H[Hold staging]
    P --> O[Observe errors, latency, safety and cost]
    O --> B{Regression?}
    B -->|Yes| RB[Rollback compatible application version]
    B -->|No| E[Record deployment evidence]
```

| Gate | Required evidence | Boundary |
| --- | --- | --- |
| Pull request | Offline tests/evals, lint/types where configured, reviewed diff | No production secrets in fork or untrusted PR jobs |
| Container | Build and synthetic functional request | Open port alone is insufficient |
| Live evals | Explicit opt-in, provider key, model and spend limits | Separate from default offline CI |
| Staging | Immutable artifact, readiness and end-to-end synthetic check | Failure stops promotion |
| Production | Approved promotion, scoped deployment identity, recovery plan | Deploy same tested image; do not rebuild |
| Recovery | Known-good digest and migration compatibility | Destructive migrations require a separate recovery plan |

Future CI should use narrowly scoped permissions and pinned actions. Prefer short-lived federated cloud identity where supported. Do not create workflows, publish images, or deploy merely because this document describes the process.

## Laptop handoff routine
Finish one increment → run relevant checks → update learning log/ADRs/flows → review diff → commit and push the shared branch → on the second laptop fetch and resume from [entrypoint.md](entrypoint.md). Preserve local changes before synchronization; never copy credentials or virtual environments through Git.
