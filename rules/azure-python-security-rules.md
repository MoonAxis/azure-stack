# Azure Python Security Rules

> Enforced by: `azure-python-security-auditor` (mandatory gate before merge)
> Reviewed by: `azure-python-code-reviewer` (BLOCKER severity on violation)

## Identity & Access

- Every Azure service client MUST authenticate via `DefaultAzureCredential`. Connection string auth is REJECTED.
- Prefer system-assigned managed identity over user-assigned unless the identity is shared across multiple services.
- RBAC assignments must be scoped to the minimum required role — never use `Owner` or `Contributor` when a data-plane role suffices.
- Python code must never construct credential objects other than `DefaultAzureCredential` without explicit justification in a comment.

## Secrets

- All secrets must reside in Azure Key Vault — never in environment variables, config files, or source code.
- Key Vault must have soft-delete and purge protection enabled.
- Secrets must have defined rotation policies (maximum 90-day validity for production credentials).
- `azure-python-security-auditor` must scan all `.py` files for hardcoded strings matching secret patterns before approving.

## PII & Data Protection

- Every Pydantic model storing PII fields must be annotated with a `# PII` comment and have a documented retention strategy.
- PII fields must be encrypted at rest. Document the encryption method in the ADR.
- Data residency must be confirmed before selecting an Azure region. EU data must remain in EU regions.
- `azure-python-solution-architect` must include a PII data flow diagram in the architecture document for any feature touching personal data.

## AI Compliance (EU AI Act)

- Every AI model decision pipeline that affects humans MUST have an audit log entry including: input hash, model version, output, confidence score, timestamp, and user identity.
- High-risk AI systems must include human-in-the-loop checkpoints — `azure-python-security-auditor` blocks merge if absent.
- Bias audit results must be documented and attached to the ADR by `azure-python-solution-architect`.
- Model explainability outputs must be logged and accessible via Application Insights.

## Network & API Security

- All FastAPI endpoints must declare a security dependency (`Depends(verify_api_key)` or OAuth2 equivalent).
- Rate limiting must be applied to all public-facing endpoints.
- CORS must be explicitly configured — never use `allow_origins=["*"]` in production.
- All inter-service communication must use HTTPS/TLS — `azure-python-code-reviewer` flags HTTP URLs as BLOCKER.

## Audit & Compliance

- All privileged operations must emit a structured audit log event to Application Insights via OpenTelemetry.
- Dead-letter queues must be monitored and alerted on (threshold: >10 messages).
- Infrastructure changes must be tracked in Terraform — no manual Azure portal changes in production.
- If infrastructure provisioning is required, delegate to `azure-terraform-*` agents — Python agents must not author Terraform code directly.
