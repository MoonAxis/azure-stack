# Azure Coding Standards

## Authentication
- Always use `DefaultAzureCredential` for Azure service clients. Never use connection strings or hardcoded keys.
- Every Azure SDK client instantiation must pass a credential obtained from `DefaultAzureCredential()`.
- Service principals are only permitted when managed identity is not available — document the reason in a comment.

## Secrets Management
- All secrets, API keys, and connection strings must be stored in Azure Key Vault.
- Never read secrets from environment variables directly — use App Configuration Key Vault references.
- Never log, print, or include secrets in error messages.

## Python Style
- All public functions and classes must have type annotations.
- Use Pydantic v2 models for all request/response schemas and data contracts.
- Follow the Base / Create / Update / Response / InDB model hierarchy for every entity.
- Async functions (`async def`) are required for all Azure SDK calls — never use sync SDK methods in a FastAPI context.

## Error Handling
- Never swallow exceptions silently. Always log with structured context (operation_id, tenant_id).
- Use `azure.core.exceptions.HttpResponseError` for Azure SDK error handling.
- Return consistent error response shapes using the project's `ErrorResponse` Pydantic model.

## Naming Conventions
- Azure resources: `{project}-{service}-{env}` (e.g., `myapp-cosmos-dev`)
- Python modules: snake_case
- Pydantic models: PascalCase
- FastAPI routers: kebab-case URL paths

## Dependencies
- Pin all Azure SDK package versions in `requirements.txt`.
- Prefer `azure-identity` over any service-specific auth package.
- Do not import `os.environ` directly for config — use a Pydantic `Settings` model with `pydantic-settings`.
