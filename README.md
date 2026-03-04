# azure-stack

A Claude Code plugin for building cloud-native applications on Azure. Provides 58 skills, 6 specialized agents, an orchestration command, 5 MCP servers, 4 LSP servers, automated hooks, and built-in rules for Azure Python development.

**Author:** Kien Nguyen | **Owner:** MoonAxis

---

## Install

```bash
/plugin marketplace add MoonAxis/azure-stack
/plugin install azure-stack
```

---

## What's Included

### Orchestration Command

```bash
/azure-stack:azure-orchestrate <workflow-type> "<description>"
```

Chains agents into a full end-to-end pipeline. Five workflow types:

| Workflow | Pipeline |
| --- | --- |
| `feature` | Planner → Architect → TDD → Implement → [CodeReview ∥ SecurityReview] |
| `bugfix` | TDD → Implement → CodeReview |
| `refactor` | Architect → TDD → Implement → CodeReview |
| `security` | Planner → [CodeReview ∥ SecurityReview] |
| `infra` | Planner → Architect → SecurityReview |

**Examples:**

```bash
/azure-stack:azure-orchestrate feature "Add AI document evaluation pipeline using Azure AI Services and Cosmos DB"
/azure-stack:azure-orchestrate bugfix "Fix race condition in Service Bus message processing"
/azure-stack:azure-orchestrate security "Audit PII handling across all API endpoints"
/azure-stack:azure-orchestrate infra "Provision Container Apps and Key Vault for staging environment"
```

**Options:** `--plan <path>`, `--from <agent>`, `--parallel-review`, `--dry-run`

---

### Agents

| Agent | Role |
| --- | --- |
| `azure-project-planner` | Creates feature plans and task breakdowns |
| `azure-solution-architect` | Designs Azure architecture and ADRs |
| `azure-tdd-suite` | Writes failing tests (RED phase) before implementation |
| `azure-code-implementer` | Implements code to pass the TDD tests (GREEN phase) |
| `azure-python-code-reviewer` | Reviews Python code for quality, severity-rated findings |
| `azure-security-auditor` | Audits for PII, auth issues, EU AI Act compliance |

---

### Skills (58)

#### AI & Machine Learning

| Skill | Description |
| --- | --- |
| `azure-ai` | Azure AI Services general patterns |
| `azure-ai-contentsafety-py` | Content Safety API |
| `azure-ai-contentunderstanding-py` | Content Understanding API |
| `azure-ai-ml-py` | Azure Machine Learning SDK |
| `azure-ai-projects-py` | AI Projects SDK |
| `azure-ai-textanalytics-py` | Text Analytics / Language service |
| `azure-ai-transcription-py` | Speech-to-text transcription |
| `azure-ai-translation-document-py` | Document translation |
| `azure-ai-translation-text-py` | Text translation |
| `azure-ai-vision-imageanalysis-py` | Computer Vision image analysis |
| `azure-ai-voicelive-py` | Real-time voice synthesis |
| `azure-aigateway` | AI Gateway patterns |
| `microsoft-foundry` | Microsoft AI Foundry / Azure AI Studio |
| `agent-framework-azure-ai-py` | Azure AI Agent framework |
| `agents-v2-py` | Agents SDK v2 patterns |
| `hosted-agents-v2-py` | Hosted agents on Azure |

#### Storage

| Skill | Description |
| --- | --- |
| `azure-storage` | Storage account patterns |
| `azure-storage-blob-py` | Blob Storage SDK |
| `azure-storage-file-datalake-py` | Data Lake Storage Gen2 |
| `azure-storage-file-share-py` | Azure Files |
| `azure-storage-queue-py` | Queue Storage |

#### Messaging & Events

| Skill | Description |
| --- | --- |
| `azure-eventgrid-py` | Event Grid SDK |
| `azure-eventhub-py` | Event Hubs SDK |
| `azure-servicebus-py` | Service Bus SDK |
| `azure-messaging-webpubsubservice-py` | Web PubSub real-time messaging |

#### Databases

| Skill | Description |
| --- | --- |
| `azure-cosmos-db-py` | Cosmos DB SDK (modern) |
| `azure-cosmos-py` | Cosmos DB SDK (classic) |
| `azure-data-tables-py` | Azure Table Storage |
| `azure-postgres` | Azure Database for PostgreSQL |
| `azure-kusto` | Azure Data Explorer (Kusto) |
| `azure-search-documents-py` | Azure AI Search |

#### Monitoring & Observability

| Skill | Description |
| --- | --- |
| `appinsights-instrumentation` | Application Insights setup |
| `azure-monitor-ingestion-py` | Log ingestion SDK |
| `azure-monitor-opentelemetry-py` | OpenTelemetry distro for Azure |
| `azure-monitor-opentelemetry-exporter-py` | OTel exporter to Azure Monitor |
| `azure-monitor-query-py` | Log Analytics query SDK |
| `azure-observability` | Observability patterns |
| `azure-diagnostics` | Diagnostics and debugging |

#### Identity & Security

| Skill | Description |
| --- | --- |
| `azure-identity-py` | DefaultAzureCredential and identity SDK |
| `entra-app-registration` | Entra ID app registration |
| `azure-rbac` | Role-based access control |
| `azure-compliance` | Compliance frameworks |

#### Infrastructure & Deployment

| Skill | Description |
| --- | --- |
| `azure-deploy` | Deployment patterns (Bicep, ARM) |
| `azure-prepare` | Pre-deployment environment setup |
| `azure-validate` | Post-deployment validation |
| `azure-containerregistry-py` | Azure Container Registry |
| `azure-appconfiguration-py` | App Configuration service |
| `azure-keyvault-py` | Key Vault SDK |
| `azure-resource-lookup` | Resource ID and connection resolution |
| `azure-resource-visualizer` | Visualize Azure resource topology |

#### Azure Management

| Skill | Description |
| --- | --- |
| `azure-mgmt-apicenter-py` | API Center management |
| `azure-mgmt-apimanagement-py` | API Management |
| `azure-mgmt-botservice-py` | Bot Service management |
| `azure-mgmt-fabric-py` | Microsoft Fabric management |

#### Python Utilities

| Skill | Description |
| --- | --- |
| `pydantic-models-py` | Pydantic v2 model patterns |
| `fastapi-router-py` | FastAPI router patterns |
| `frontend-design-review` | Frontend design review for Azure apps |

---

### MCP Servers

Five Azure MCP servers are configured automatically on install:

| Server | Purpose | Required Env Var |
| --- | --- | --- |
| `azure-resource-lookup` | Resolve resource IDs, connection strings, RBAC | `AZURE_SUBSCRIPTION_ID`, `AZURE_TENANT_ID` |
| `azure-keyvault` | Read secrets from Key Vault during development | `AZURE_KEYVAULT_URL`, `AZURE_TENANT_ID` |
| `azure-ai-foundry` | Query AI Foundry projects and model deployments | `AZURE_AI_PROJECT_CONNECTION_STRING` |
| `azure-cosmos-db` | Read and query Cosmos DB containers | `AZURE_COSMOS_ENDPOINT` |
| `azure-monitor` | Query Application Insights and logs via KQL | `AZURE_MONITOR_WORKSPACE_ID` |

---

### LSP Servers

| Server | Languages | Tool |
| --- | --- | --- |
| Pyright | Python | Strict type checking with Azure SDK symbol resolution |
| Ruff | Python | Fast linting (PEP 8, imports, security, annotations) |
| YAML | YAML / ARM templates / Azure Pipelines | Schema validation |
| Bicep | Bicep | Infrastructure-as-code authoring |

---

### Rules

Rules are automatically injected into every conversation:

- **azure-coding-standards** — `DefaultAzureCredential` enforcement, Key Vault for secrets, naming conventions, async patterns
- **azure-security-rules** — No hardcoded secrets, PII handling, EU AI Act compliance checks
- **azure-agent-pipeline-rules** — Agent handoff formats, blocker escalation, artefact persistence

---

### Hooks

Automated actions triggered by development events:

| Event | Trigger | Action |
| --- | --- | --- |
| `on_file_save` | `src/**/*.py` | Run code review (MAJOR threshold) |
| `on_pr_open` | Any PR | Run full code review (MINOR threshold) |
| `on_pr_open` | Any PR | Run security audit |
| `on_branch_create` | `feature/**` branches | Prompt planning via planner agent |
| `pre_commit` | `src/**/*.py` | Block commit if security audit returns REJECTED |
| `on_test_fail` | Any test failure | Invoke implementer to attempt fix (max 2 retries) |

---

## Configuration

Default settings in `settings.json` — override per project:

```json
{
  "azure": {
    "defaultRegion": "eastus",
    "defaultEnvironment": "dev",
    "identity": { "preferManagedIdentity": true }
  },
  "workflow": {
    "defaultType": "feature",
    "parallelReview": true,
    "haltOnBlocker": true
  },
  "security": {
    "requireSecurityReviewOnPR": true,
    "euAiActAuditRequired": true,
    "requireKeyVaultForSecrets": true
  },
  "testing": {
    "requireRedPhaseBeforeImplement": true,
    "minCoverage": 80
  }
}
```

---

## Requirements

- Node.js (for MCP servers via `npx`)
- Python with `pyright-langserver` and `ruff-lsp` (for LSP)
- `bicep-langserver` and `yaml-language-server` (for infrastructure files)
- Azure CLI authenticated (`az login`) or environment variables set for MCP servers

---

## License

MIT License

Copyright (c) 2025 MoonAxis

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
