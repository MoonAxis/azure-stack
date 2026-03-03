# Azure Security Rules

## Identity & Access
- Every Azure service client MUST authenticate via `DefaultAzureCredential`. Connection string auth is REJECTED.
- Prefer system-assigned managed identity over user-assigned unless the identity is shared across multiple services.
- RBAC assignments must be scoped to the minimum required role — never use `Owner` or `Contributor` when a data-plane role suffices.

## Secrets
- All secrets must reside in Azure Key Vault — never in environment variables, config files, or source code.
- Key Vault must have soft-delete and purge protection enabled.
- Secrets must have defined rotation policies (maximum 90-day validity for production credentials).

## PII & Data Protection
- Every entity storing PII must have a documented retention and deletion strategy.
- PII fields must be encrypted at rest. Document the encryption method in the ADR.
- Data residency must be confirmed before selecting an Azure region. EU data must remain in EU regions.

## AI Compliance (EU AI Act)
- Every AI model decision pipeline that affects humans MUST have an audit log entry including: input hash, model version, output, confidence score, timestamp, and user identity.
- High-risk AI systems must include human-in-the-loop checkpoints.
- Bias audit results must be documented and attached to the ADR.
- Model explainability outputs must be logged and accessible.

## Network & API Security
- All API endpoints must be protected by authentication (`Depends(verify_api_key)` or equivalent).
- Rate limiting must be applied to all public-facing endpoints.
- CORS must be explicitly configured — never use `allow_origins=["*"]` in production.
- All inter-service communication must use HTTPS/TLS.

## Audit & Compliance
- All privileged operations must emit an audit log event to Application Insights.
- Dead-letter queues must be monitored and alerted on (threshold: >10 messages).
- Infrastructure changes must be tracked in IaC (Bicep/Terraform) — no manual Azure portal changes in production.
