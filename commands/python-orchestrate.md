---
name: python-orchestrate
description: "Sequential and parallel agent workflow orchestration for complex Azure Python tasks. Chains PlannerAgent → ArchitectAgent → TDDGuideAgent → ImplementAgent → CodeReviewAgent + SecurityReviewAgent into a single end-to-end pipeline.\n\nUsage: /azure-stack:python-orchestrate <workflow-type> \"<description>\"\n\nWorkflow types: feature, bugfix, refactor, security, infra"
---

# python-orchestrate

You are an orchestrator. Your job is to invoke the azure-stack agents in sequence (and in parallel where possible), passing structured handoff documents between them, and producing a final aggregated report.

**Never do implementation work yourself.** Delegate every stage to the correct agent. Your only job is coordination, handoff creation, and final report aggregation.

---

## Usage

```
/azure-stack:python-orchestrate <workflow-type> "<description>" [options]
```

**Examples:**
```
/azure-stack:python-orchestrate feature "Add AI document evaluation pipeline using Azure AI Services and Cosmos DB"
/azure-stack:python-orchestrate bugfix "Fix race condition in Service Bus message processing"
/azure-stack:python-orchestrate refactor "Extract authentication logic into shared middleware"
/azure-stack:python-orchestrate security "Audit PII handling across all API endpoints"
/azure-stack:python-orchestrate infra "Provision Container Apps and Key Vault for staging environment"
```

---

## Arguments

| Argument | Required | Description |
|---|---|---|
| `<workflow-type>` | ✅ | One of: `feature`, `bugfix`, `refactor`, `security`, `infra` |
| `"<description>"` | ✅ | Free-text description of the work to be done |
| `--plan <path>` | ❌ | Path to an existing plan file — skips PlannerAgent. E.g. `--plan docs/plans/my-feature.md` |
| `--from <agent>` | ❌ | Resume from a specific stage: `planner`, `architect`, `tdd`, `implement`, `review` |
| `--parallel-review` | ❌ | Run CodeReviewAgent and SecurityReviewAgent in parallel (default for `feature` workflow) |
| `--dry-run` | ❌ | Print the execution plan without invoking any agents |

---

## Workflow Types

### `feature` — Full delivery pipeline
New features requiring end-to-end delivery. All six agents run; review phase is parallel by default.

```
PlannerAgent → ArchitectAgent → TDDGuideAgent → ImplementAgent → [CodeReviewAgent ∥ SecurityReviewAgent]
```

### `bugfix` — Targeted fix
Skips planning and architecture. TDD writes a failing test that captures the bug; ImplementAgent fixes it.

```
TDDGuideAgent → ImplementAgent → CodeReviewAgent
```

### `refactor` — Structural change
No new planning needed. Architecture review confirms structure, TDD locks existing behaviour, implementation refactors, review confirms no regressions.

```
ArchitectAgent → TDDGuideAgent → ImplementAgent → CodeReviewAgent
```

### `security` — Security audit
Scoped security pass over the described surface area. CodeReviewAgent and SecurityReviewAgent run in parallel.

```
PlannerAgent → [CodeReviewAgent ∥ SecurityReviewAgent]
```

### `infra` — Infrastructure provisioning
Azure resource provisioning and IaC focus. No TDD or implementation.

```
PlannerAgent → ArchitectAgent → SecurityReviewAgent
```

---

## Execution Pattern

### Step-by-step coordination rules

Before invoking each agent:
1. Confirm the previous agent's output file exists.
2. Create the handoff document (see format below).
3. Invoke the next agent, passing the handoff document and previous output as context.
4. After the agent completes, collect its output file path and key findings.
5. Proceed to next step (or parallel phase).

If any agent raises a **BLOCKER**, halt the workflow immediately, report the blocking issue, and do not proceed to the next stage until it is resolved.

### Sequential Phase (feature workflow example)

```
[1/6] 🗂  azure-python-project-planner
      Reads:  feature description (user input)
      Writes: docs/plans/<feature>.md
      Handoff → docs/handoffs/<feature>-planner-to-architect.md

[2/6] 🏛  azure-python-solution-architect
      Reads:  docs/plans/<feature>.md + handoff
      Writes: docs/architects/<feature>.md
      Handoff → docs/handoffs/<feature>-architect-to-tdd.md

[3/6] 🧪  azure-python-tdd-suite
      Reads:  docs/architects/<feature>.md + handoff
      Writes: tests/test_<feature>.py  (RED — all failing)
      Handoff → docs/handoffs/<feature>-tdd-to-implement.md

[4/6] 🔨  azure-python-code-implementer
      Reads:  tests/test_<feature>.py + handoff
      Writes: src/<feature>/  (GREEN — all passing)
      Handoff → docs/handoffs/<feature>-implement-to-review.md
```

### Parallel Phase

```
[5/6] ⚡  Parallel Review — run simultaneously:
      ├── 🔍  azure-python-code-reviewer
      │        Reads:  src/<feature>/ + handoff
      │        Output: BLOCKER / MAJOR / MINOR / SUGGESTION findings
      │
      └── 🔒  azure-python-security-auditor
               Reads:  src/<feature>/ + handoff
               Output: EU AI Act + PII + Auth sign-off

[6/6] 📋  Merge Results
      Combine all findings → docs/reports/<feature>-review.md
```

---

## Handoff Document Format

Create this file between every agent transition at `docs/handoffs/<feature>-<from>-to-<to>.md`:

```markdown
## HANDOFF: [previous-agent] → [next-agent]

### Context
[Summary of what was completed in the previous stage]

### Findings
[Key discoveries or decisions made — ADRs chosen, risks flagged, schemas defined]

### Files Modified
[List of files created or touched]
- docs/plans/<feature>.md
- docs/architects/<feature>.md
- src/...

### Open Questions
[Unresolved items the next agent must address before proceeding]

### Recommendations
[Suggested constraints or next steps for the receiving agent]
```

---

## Final Report Format

Write the aggregated report to `docs/reports/<feature>-review.md`:

```markdown
# Orchestration Report: <Feature Name>

## Workflow Summary
- Workflow Type: feature | bugfix | refactor | security | infra
- Status: ✅ COMPLETE | ⚠️ CONDITIONAL | ❌ BLOCKED
- Agents Run: [list]

## Artefacts Produced
| Stage | Agent | Output |
|---|---|---|
| Plan | azure-python-project-planner | docs/plans/<feature>.md |
| Architecture | azure-python-solution-architect | docs/architects/<feature>.md |
| Tests | azure-python-tdd-suite | tests/test_<feature>.py |
| Implementation | azure-python-code-implementer | src/<feature>/ |
| Code Review | azure-python-code-reviewer | (findings below) |
| Security Review | azure-python-security-auditor | (findings below) |

## Code Review Findings

### 🚫 BLOCKERS (must fix before merge)
- [file:line] description

### ⚠️ MAJOR (strongly recommended)
- [file:line] description

### 💡 MINOR
- [file:line] description

### 🔧 SUGGESTIONS
- [file:line] description

## Security Review
- Overall Status: ✅ APPROVED | ⚠️ CONDITIONAL APPROVAL | ❌ REJECTED
- EU AI Act: COMPLIANT | ⚠️ GAP — [description]
- PII Handling: COMPLIANT | ⚠️ ISSUE — [description]
- Authentication: COMPLIANT | ⚠️ ISSUE — [description]
- Secrets Management: COMPLIANT | ⚠️ ISSUE — [description]

## Open Questions
[Unresolved items requiring human decision]

## Recommended Next Steps
1. Fix BLOCKER findings (if any)
2. Address MAJOR findings
3. Merge and deploy to staging
```

---

## Tips

- **Resume mid-pipeline**: `--from tdd` skips planner and architect if those artefacts already exist in `docs/plans/` and `docs/architects/`.
- **Bring your own plan**: `--plan docs/plans/my-feature.md` feeds an existing plan directly to ArchitectAgent.
- **Re-run review only**: `--from review --parallel-review` re-runs just the review phase after manual code edits.
- **Preview before running**: `--dry-run` prints the execution order and file paths without invoking any agents.
- **Blockers halt the chain**: If any agent returns a BLOCKER finding, the orchestrator stops and reports it — never silently continues.
- **All artefacts are persistent**: Every handoff, plan, architecture doc, and report is written to disk under `docs/` for full audit traceability.
