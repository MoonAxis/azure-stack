# Azure Python Agent Pipeline Rules

> Governs all `azure-python-*` agents. These rules are non-negotiable and override any agent-level defaults.

## Pipeline Order (Non-Negotiable)

1. `azure-python-project-planner` must always run before `azure-python-solution-architect`.
2. `azure-python-solution-architect` must always run before `azure-python-tdd-suite`.
3. `azure-python-tdd-suite` must produce failing tests (RED) before `azure-python-code-implementer` runs.
4. `azure-python-code-implementer` must make all tests pass (GREEN) before any review agent runs.
5. `azure-python-security-auditor` must sign off before any code is merged to main.

## Handoff Requirements

- Every agent transition must produce a handoff document at `docs/handoffs/<feature>-<from>-to-<to>.md`.
- Handoff documents must include: Context, Findings, Files Modified, Open Questions, Recommendations.
- No agent may proceed without reading the previous agent's output file and handoff document.
- If a handoff document is missing, the receiving agent must halt and request it — never infer from context alone.

## Plan & Architecture Persistence

- `azure-python-project-planner` must always write its output to `docs/plans/<feature>.md`.
- `azure-python-solution-architect` must always read from `docs/plans/<feature>.md` and write to `docs/architects/<feature>.md`.
- Plans and architecture documents must not be deleted or overwritten — create a new versioned file instead.

## Blocking Rules

- A BLOCKER finding from `azure-python-code-reviewer` halts the pipeline until resolved — `azure-python-code-implementer` must fix before merge.
- A REJECTED status from `azure-python-security-auditor` halts the pipeline and blocks merge.
- A ⚠️ EU AI ACT GAP finding requires explicit human sign-off before the pipeline can continue.
- No agent may skip a stage — `--from` flag only valid when prior stage artefacts already exist on disk.

## Parallel Review

- `azure-python-code-reviewer` and `azure-python-security-auditor` may run in parallel after `azure-python-code-implementer` completes.
- Their findings must be merged into a single report at `docs/reports/<feature>-review.md` before delivery.

## Quality Gates

- Every `[API]` and `[AZURE_SERVICE]` task must have a corresponding `[TEST]` task — no exceptions.
- Minimum test coverage: 80% for all new code (enforced by `azure-python-tdd-suite`).
- All Pydantic models must follow the `Base` / `Create` / `Update` / `Response` / `InDB` hierarchy.
- All ADRs must document ≥2 alternatives considered (enforced by `azure-python-solution-architect`).
