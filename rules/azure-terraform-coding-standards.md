# Azure Terraform Coding Standards

## Authentication & Identity
- All Azure provider authentication must use `use_oidc = true` or `use_azure_cli = true` — never hardcode credentials.
- Managed identity is the preferred authentication method for Azure resources over service principals.
- Service principals must use federated credentials (OIDC) — client secret auth is only permitted when OIDC is unavailable and must be documented.
- All `azurerm_role_assignment` blocks must specify the minimum required built-in role — never assign `Owner` or `Contributor` when a data-plane role suffices.

## Secrets Management
- Never store secrets, connection strings, or API keys in Terraform `.tfvars` files or state.
- All secrets must be provisioned into Azure Key Vault. Reference them via `azurerm_key_vault_secret` data sources.
- Key Vault must have `soft_delete_retention_days` ≥ 7 and `purge_protection_enabled = true` in production.
- `sensitive = true` must be set on all Terraform output values that contain secrets or credentials.

## Module Structure
- Every infrastructure component must be encapsulated in a reusable module under `terraform/modules/<component>/`.
- Every module must have: `main.tf`, `variables.tf`, `outputs.tf`, `README.md`.
- Root modules (`terraform/environments/<env>/`) call child modules — no resource blocks in root except backend and provider.
- Module source references must pin to a specific git tag or registry version — never use `latest` or an unpinned ref.

## Naming Conventions
- All Azure resources: `{project}-{service}-{env}` (e.g., `myapp-cosmos-prod`).
- Terraform resource labels: `snake_case` (e.g., `azurerm_storage_account.app_storage`).
- Variables: `snake_case` with descriptive names (e.g., `storage_account_replication_type`).
- Outputs: `snake_case`, prefixed with resource type (e.g., `storage_account_id`, `keyvault_uri`).
- Locals: `snake_case`, group related values with a shared prefix (e.g., `tags_common`, `tags_env`).

## Tagging
- Every resource must include a `tags` block inheriting from a common locals map.
- Required tags: `environment`, `owner`, `cost-center`, `managed-by = "terraform"`, `project`.
- Tag values must not be hardcoded inline — define all tag values in `locals.tf` or passed via variables.

## State Management
- Remote state backend must use `azurerm` with `storage_account_name`, `container_name`, `key`, and `use_oidc = true`.
- State must be isolated per environment — never share a state file between dev/staging/prod.
- State locking must be enabled — use the `azurerm` backend's built-in lock via Azure Blob leases.
- Never run `terraform state mv` or `terraform state rm` without a documented justification and backup.

## Variable & Output Hygiene
- All input variables must have a `description` and `type`. Sensitive variables must set `sensitive = true`.
- Variables without a `default` are required — document this explicitly in `variables.tf`.
- Outputs exposed to other modules or root must document what they expose in `description`.
- Never use `any` as a variable type — be explicit (`string`, `number`, `bool`, `list(string)`, `map(string)`, etc.).

## Provider Configuration
- Pin all provider versions in `versions.tf` using `~>` constraints (e.g., `~> 3.0`).
- The `azurerm` provider `features {}` block must always be present, even if empty.
- Use `provider_meta` for module-level provider aliasing when managing multiple subscriptions.
- Never use `terraform {}` `required_providers` with an unpinned version.

## Formatting & Quality
- All Terraform files must pass `terraform fmt` with zero diffs before commit.
- All Terraform configurations must pass `terraform validate` before any plan or apply.
- Use `terraform-docs` to auto-generate module README tables from variables and outputs.
- Avoid `count` for resources that have meaningful identity — prefer `for_each` with a map for stable addressing.
