# Azure Agent Pipeline Rules

## Pipeline Order (Non-Negotiable)
1. `azure-project-planner` must always run before `azure-solution-architect`.
2. `azure-solution-architect` must always run before `azure-tdd-suite`.
3. `azure-tdd-suite` must produce failing tests (RED) before `azure-code-implementer` runs.
4. `azure-code-implementer` must make all tests pass (GREEN) before any review agent runs.
5. `azure-security-auditor` must sign off before any code is merged to main.

## Handoff Requirements
- Every agent transition must produce a handoff document at `docs/handoffs/<feature>-<from>-to-<to>.md`.
- Handoff documents must include: Context, Findings, Files Modified, Open Questions, Recommendations.
- No agent may proceed without reading the previous agent's output file and handoff document.

## Plan & Architecture Persistence
- `azure-project-planner` must always write its output to `docs/plans/<feature>.md`.
- `azure-solution-architect` must always read from `docs/plans/<feature>.md` and write to `docs/architects/<feature>.md`.
- Plans and architecture documents must not be deleted or overwritten without creating a new versioned file.

## Blocking Rules
- A BLOCKER finding from `azure-python-code-reviewer` halts the pipeline until resolved.
- A REJECTED status from `azure-security-auditor` halts the pipeline and blocks merge.
- A ⚠️ EU AI ACT GAP finding requires explicit human sign-off before the pipeline can continue.

## Parallel Review
- `azure-python-code-reviewer` and `azure-security-auditor` may run in parallel after implementation.
- Their findings must be merged into a single report at `docs/reports/<feature>-review.md` before delivery.

## Quality Gates
- Every [API] and [AZURE_SERVICE] task must have a corresponding [TEST] task — no exceptions.
- Minimum test coverage: 80% for all new code.
- All Pydantic models must follow the Base/Create/Update/Response/InDB hierarchy.
- All ADRs must document ≥2 alternatives considered.
