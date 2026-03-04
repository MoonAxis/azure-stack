# Azure Terraform Security Rules

## Identity & Access
- Every Azure resource that supports managed identity MUST have it enabled — document any exception in an ADR.
- RBAC assignments must be scoped to the smallest applicable scope (resource > resource group > subscription).
- Never assign `Owner` or `Contributor` to a service principal used by Terraform automation — use custom roles or `Contributor` at resource group scope only.
- All `azurerm_role_assignment` blocks must be reviewed by `azure-terraform-auditor` — overprivileged assignments are BLOCKERS.
- Entra ID (Azure AD) tenant ID and subscription ID must be sourced from `data "azurerm_client_config"` — never hardcoded.

## Secrets & Key Vault
- All secrets must reside in Azure Key Vault — never in Terraform state, `.tfvars`, or environment variables.
- Key Vault must enforce: `soft_delete_retention_days ≥ 7`, `purge_protection_enabled = true`, `sku_name = "premium"` for HSM-backed keys in production.
- Key Vault access must use RBAC (`enable_rbac_authorization = true`) — not legacy Access Policies in new deployments.
- Secrets must have defined expiry (`expiration_date`) when provisioned via `azurerm_key_vault_secret`.

## Network Security
- All PaaS services (Storage, Cosmos DB, Key Vault, SQL, etc.) must use private endpoints in production — public network access must be explicitly disabled.
- Network Security Groups must have `deny all inbound` as a base rule with explicit allow rules documented.
- Azure Firewall or Application Gateway WAF must protect all public-facing ingress points.
- No resource may have `public_network_access_enabled = true` in a production environment without a documented exception in the ADR and MAJOR finding raised by auditor.
- VNet integration is required for all App Services and Function Apps accessing private resources.

## Encryption
- All storage accounts must set `min_tls_version = "TLS1_2"` and `https_traffic_only_enabled = true`.
- All databases and storage resources must have encryption at rest enabled — document whether keys are Microsoft-managed or customer-managed.
- Customer-managed keys (CMK) are required for regulated workloads (HIPAA, PCI-DSS). Document the Key Vault key reference in the ADR.
- All data in transit must use TLS 1.2 or higher — TLS 1.0 and 1.1 are REJECTED.

## Compliance & Governance
- All resources must have `managed-by = "terraform"` and `environment` tags — untagged resources are a MAJOR finding.
- Data residency must be verified before selecting an Azure region. EU personal data must remain in EU regions.
- Resources subject to HIPAA, PCI-DSS, SOC2, or GDPR must have compliance documentation in the ADR.
- Azure Policy assignments that restrict resource configuration must be accounted for in Terraform code — never design infrastructure that relies on bypassing Policy.
- Diagnostic settings (`azurerm_monitor_diagnostic_setting`) must be configured for all control-plane resources in production.

## Infrastructure Hardening
- Azure Defender / Microsoft Defender for Cloud must be enabled for relevant resource types in production subscriptions.
- Storage accounts must set `allow_nested_items_to_be_public = false` and `shared_access_key_enabled = false` unless explicitly required.
- SQL and Cosmos DB must have auditing enabled with a Log Analytics workspace destination.
- Container registries must set `admin_enabled = false` and use RBAC for image push/pull.
- `azurerm_kubernetes_cluster` must enable RBAC (`role_based_access_control_enabled = true`), Azure AD integration, and disable local accounts.

## Audit & Change Control
- All infrastructure changes must flow through the Terraform pipeline — no manual Azure portal changes in production (BLOCKER if detected).
- Terraform plan output must be reviewed by `azure-terraform-risk-analyzer` before every production apply.
- State file access must be restricted via RBAC — only the CI/CD service principal and designated operators may read or write state.
- Every `terraform destroy` on a production resource requires a documented change request and dual approval.
- `prevent_destroy = true` lifecycle rule must be set on all stateful production resources (databases, storage, Key Vault).
