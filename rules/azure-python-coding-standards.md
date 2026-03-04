# Azure Python Coding Standards

> Applies to: `azure-python-project-planner`, `azure-python-solution-architect`, `azure-python-tdd-suite`, `azure-python-code-implementer`, `azure-python-code-reviewer`, `azure-python-security-auditor`

## Authentication

- Always use `DefaultAzureCredential` for Azure service clients. Never use connection strings or hardcoded keys.
- Every Azure SDK client instantiation must pass a credential obtained from `DefaultAzureCredential()`.
- Service principals are only permitted when managed identity is not available — document the reason in a comment.

## Secrets Management

- All secrets, API keys, and connection strings must be stored in Azure Key Vault.
- Never read secrets from environment variables directly — use App Configuration Key Vault references.
- Never log, print, or include secrets in error messages.

## Python Style

- All public functions and classes must have type annotations (enforced by Pyright strict mode).
- Use Pydantic v2 models for all request/response schemas and data contracts.
- Follow the `Base` / `Create` / `Update` / `Response` / `InDB` model hierarchy for every entity.
- Async functions (`async def`) are required for all Azure SDK calls — never use sync SDK methods in a FastAPI context.
- Use `async with` for Azure SDK clients that support context managers.

## Error Handling

- Never swallow exceptions silently. Always log with structured context (`operation_id`, `tenant_id`).
- Use `azure.core.exceptions.HttpResponseError` for Azure SDK error handling.
- Return consistent error response shapes using the project's `ErrorResponse` Pydantic model.
- Re-raise after logging — do not convert exceptions to silent `None` returns.

## Naming Conventions

- Azure resources: `{project}-{service}-{env}` (e.g., `myapp-cosmos-dev`)
- Python modules: `snake_case`
- Pydantic models: `PascalCase`
- FastAPI routers: `kebab-case` URL paths
- Azure SDK clients: `{service}_client` (e.g., `cosmos_client`, `blob_client`)

## Dependencies

- Pin all Azure SDK package versions in `requirements.txt`.
- Prefer `azure-identity` over any service-specific auth package.
- Do not import `os.environ` directly for config — use a Pydantic `Settings` model with `pydantic-settings`.
- Group imports: stdlib → third-party → `azure-*` → local.
