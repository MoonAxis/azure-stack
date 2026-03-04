# azure-stack

A Claude Code plugin for building cloud-native applications on Azure. Covers both **Python** and **Terraform** workflows with 69 skills, 12 specialized agents, 2 orchestration commands, smart file-type routing hooks, governance audit, and session tooling.

**Author:** Kien Nguyen | **Owner:** MoonAxis | **Version:** 1.1.0

---

## Install

```bash
/plugin marketplace add MoonAxis/azure-stack
/plugin install azure-stack
```

---

## What's Included

### Orchestration Commands

Two commands — one per stack:

#### Python pipeline

```bash
/azure-stack:python-orchestrate <workflow-type> "<description>"
```

| Workflow | Pipeline |
| --- | --- |
| `feature` | Planner → Architect → TDD → Implement → [CodeReview ∥ SecurityReview] |
| `bugfix` | TDD → Implement → CodeReview |
| `refactor` | Architect → TDD → Implement → CodeReview |
| `security` | Planner → [CodeReview ∥ SecurityReview] |
| `infra` | Planner → Architect → SecurityReview |

#### Terraform pipeline

```bash
/azure-stack:terraform-orchestrate <workflow-type> "<description>"
```

**Options (both commands):** `--plan <path>`, `--from <agent>`, `--parallel-review`, `--dry-run`

---

### Agents (12)

#### azure-python-* (6)

| Agent | Role |
| --- | --- |
| `azure-python-project-planner` | Creates feature plans and task breakdowns |
| `azure-python-solution-architect` | Designs Azure architecture and ADRs |
| `azure-python-tdd-suite` | Writes failing tests (RED phase) before implementation |
| `azure-python-code-implementer` | Implements code to pass the TDD tests (GREEN phase) |
| `azure-python-code-reviewer` | Reviews Python code for quality, severity-rated findings |
| `azure-python-security-auditor` | Audits for PII, auth issues, EU AI Act compliance |

#### azure-terraform-* (6)

| Agent | Role |
| --- | --- |
| `azure-terraform-planner` | Plans Terraform feature work and IaC task breakdown |
| `azure-terraform-architect` | Designs Terraform module structure and ADRs |
| `azure-terraform-generator` | Generates Terraform code using Azure Verified Modules |
| `azure-terraform-risk-analyzer` | Reviews Terraform plans for drift and risk |
| `azure-terraform-auditor` | Security audit for Terraform configurations |
| `azure-terraform-deployment-guide` | Guides safe Terraform deployment execution |

---

### Skills (69)

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
| `ai-prompt-engineering-safety-review` | Analyze prompts for safety, bias, and security vulnerabilities |
| `agentic-eval` | Self-critique, evaluator-optimizer, and LLM-as-judge evaluation patterns |
| `agent-governance` | Governance, safety, and trust controls for AI agent systems |

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
| `azure-resource-health-diagnose` | Analyze resource health, diagnose issues, and create remediation plans |

#### Identity & Security

| Skill | Description |
| --- | --- |
| `azure-identity-py` | DefaultAzureCredential and identity SDK |
| `entra-app-registration` | Entra ID app registration |
| `azure-rbac` | Role-based access control patterns |
| `azure-role-selector` | Least-privilege role guidance with Bicep and CLI output |
| `azure-compliance` | Compliance frameworks |

#### Infrastructure & Deployment

| Skill | Description |
| --- | --- |
| `azure-deploy` | Deployment patterns |
| `azure-prepare` | Pre-deployment environment setup |
| `azure-validate` | Post-deployment validation |
| `azure-deployment-preflight` | Preflight validation (what-if, syntax, permissions) before any deployment |
| `azure-containerregistry-py` | Azure Container Registry |
| `azure-appconfiguration-py` | App Configuration service |
| `azure-keyvault-py` | Key Vault SDK |
| `azure-resource-lookup` | Resource ID and connection resolution |
| `azure-resource-visualizer` | Visualize Azure resource topology |
| `az-cost-optimize` | Analyze IaC and resources for cost savings, creates GitHub issues |
| `azure-devops-cli` | Azure DevOps CLI — pipelines, repos, work items, PRs |

#### Terraform

| Skill | Description |
| --- | --- |
| `import-infrastructure-as-code` | Reverse-engineer live Azure resources into Terraform using Azure Verified Modules |
| `terraform-azurerm-set-diff-analyzer` | Distinguish false-positive diffs from real changes in Terraform plans |

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

#### Documentation

| Skill | Description |
| --- | --- |
| `microsoft-docs` | Query Microsoft Learn, Azure, .NET, Aspire, VS Code, and GitHub docs |

---

### MCP Servers

Five Azure MCP servers configured automatically on install:

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

#### Azure Python

- **azure-python-coding-standards** — `DefaultAzureCredential`, Pydantic v2, async SDK, error handling, naming
- **azure-python-security-rules** — PII handling, Key Vault, EU AI Act, FastAPI auth, audit logging
- **azure-python-agent-pipeline-rules** — Pipeline order, handoff requirements, blocking rules, quality gates

#### Azure Terraform

- **azure-terraform-coding-standards** — Module structure, AVM patterns, variable conventions
- **azure-terraform-security-rules** — Secrets, RBAC, network security, audit requirements
- **azure-terraform-pipeline-rules** — Plan → apply order, risk analysis gates, deployment safety

---

### Hooks

#### File-type routing — hooks activate based on what you're editing

| Event | Match | Agent |
| --- | --- | --- |
| `on_file_save` | `src/**/*.py` | `azure-python-code-reviewer` |
| `on_file_save` | `**/*.tf` | `azure-terraform-risk-analyzer` |
| `on_pr_open` | `**/*.py` | `azure-python-code-reviewer` + `azure-python-security-auditor` |
| `on_pr_open` | `**/*.tf` | `azure-terraform-risk-analyzer` + `azure-terraform-auditor` |
| `on_branch_create` | `feature/**` | `azure-python-project-planner` |
| `on_branch_create` | `infra/**` | `azure-terraform-planner` |
| `pre_commit` | `src/**/*.py` | `azure-python-security-auditor` (blocks on REJECTED) |
| `pre_commit` | `**/*.tf` | `azure-terraform-auditor` (blocks on REJECTED) |
| `on_test_fail` | — | `azure-python-code-implementer` |

#### Session hooks — opt-in, copy to your project

| Hook | Events | Purpose |
| --- | --- | --- |
| `session-logger` | start, end, prompt | JSON audit log of all session activity |
| `governance-audit` | start, end, prompt | Real-time threat detection on every prompt |
| `session-auto-commit` | end | Auto-commit and push all changes at session end |

**To use a session hook in your project:**

```bash
cp -r hooks/<hook-name> .github/hooks/
chmod +x .github/hooks/<hook-name>/*.sh
```

**governance-audit config:**

| Variable | Values | Default |
| --- | --- | --- |
| `GOVERNANCE_LEVEL` | `open`, `standard`, `strict`, `locked` | `standard` |
| `BLOCK_ON_THREAT` | `true`, `false` | `false` |

---

## Configuration

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
- Terraform CLI (for Terraform agents and skills)
- Azure CLI authenticated (`az login`) or environment variables set for MCP servers
- `jq` and `bc` (for governance-audit hook)

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
