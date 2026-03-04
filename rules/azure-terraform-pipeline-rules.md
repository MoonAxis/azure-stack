# Azure Terraform Pipeline Rules

## Pipeline Order (Non-Negotiable)
1. `azure-terraform-planner` must always run before `azure-terraform-architect`.
2. `azure-terraform-architect` must always run before `azure-terraform-generator`.
3. `azure-terraform-generator` must always run before `azure-terraform-auditor`.
4. `azure-terraform-auditor` must complete before `azure-terraform-risk-analyzer` or `azure-terraform-governance-validator` run.
5. `azure-terraform-governance-validator` must return APPROVED before `azure-terraform-deployment-guide` produces a deployment plan.
6. `azure-terraform-deployment-guide` is the final gate — no deployment proceeds without its sign-off.

## Handoff Requirements
- Every agent transition must produce a handoff document at `docs/handoffs/<infra>-<from>-to-<to>.md`.
- Handoff documents must include: Context, Findings, Files Modified, Open Questions, Recommendations.
- No agent may proceed without reading the previous agent's output file and its handoff document.

## Plan & Architecture Persistence
- `azure-terraform-planner` must always write its output to `docs/plans/<infra>.md`.
- `azure-terraform-architect` must always read from `docs/plans/<infra>.md` and write to `docs/architects/<infra>.md`.
- `azure-terraform-generator` must write all generated code under `terraform/<infra>/`.
- Plans, ADRs, and architecture documents must not be deleted or overwritten — create a new versioned file instead.

## Blocking Rules
- A BLOCKER finding from `azure-terraform-auditor` halts the pipeline until resolved — no deployment proceeds.
- A REJECTED status from `azure-terraform-governance-validator` halts the pipeline and blocks deployment.
- A CRITICAL risk from `azure-terraform-risk-analyzer` requires explicit human sign-off before applying.
- Any destructive change (resource deletion, replacement) on a production resource requires explicit human approval.

## Parallel Execution
- `azure-terraform-risk-analyzer` and `azure-terraform-governance-validator` may run in parallel after audit.
- Their findings must be merged into the final report at `docs/reports/<infra>-orchestration-report.md`.

## Quality Gates
- Every resource block in generated Terraform must include required tags (environment, owner, cost-center).
- All remote backends must use Azure Storage with encryption, versioning, and state locking enabled.
- No Terraform state file may be stored locally for any shared or production environment.
- Production and non-production state must always be isolated in separate backends.
- All ADRs must document ≥2 alternatives considered before a service selection is made.
- Minimum: every module must expose input variables and output values — no hardcoded resource names.
