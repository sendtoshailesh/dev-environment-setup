# Development Environment Setup Documentation

**Created**: January 16, 2026  
**Last Validated**: January 16, 2026  
**Machine**: Windows  
**Status**: ✅ All Systems Validated

---

## Validation Summary

| Component | Status | Details |
|-----------|--------|---------|
| Git | ✅ Validated | v2.52.0.windows.1 |
| GitHub CLI | ✅ Validated | v2.83.2, logged in as sendtoshailesh |
| GitHub Copilot CLI | ✅ Validated | v1.2.0 |
| Azure CLI | ✅ Validated | v2.80.0 |
| Azure Subscription | ✅ Validated | ME-MngEnvMCAP078213-shailmishra-1 |
| AI Hub | ✅ Validated | ai-hub-shailesh (West US 2) |
| AI Project | ✅ Validated | ai-project-shailesh (West US 2) |
| Azure OpenAI | ✅ Validated | aoai-shailesh (East US) |
| GPT-4o Deployment | ✅ Validated | 10K TPM, GlobalStandard |

---

## 1. GitHub & Git Configuration

### Tool Versions (Validated ✅)
| Tool | Version | Status |
|------|---------|--------|
| Git | 2.52.0.windows.1 | ✅ Installed |
| GitHub CLI (gh) | 2.83.2 | ✅ Installed |
| GitHub Copilot CLI | 1.2.0 | ✅ Installed |

### Git Global Configuration (Validated ✅)
```ini
user.name=sendtoshailesh
user.email=sendtoshailesh@users.noreply.github.com
credential.helper=manager
init.defaultbranch=main
core.autocrlf=true
```

**Config File**: `C:\Users\shailmishra\.gitconfig`

### GitHub Authentication (Validated ✅)
| Account | Status | Protocol | Token Scopes |
|---------|--------|----------|--------------|
| **sendtoshailesh** | ✅ Active | HTTPS | gist, read:org, repo, workflow |
| shailmishra_MSDEMO | Available | HTTPS | gist, read:org, repo, workflow |

### Copilot CLI Aliases (PowerShell Profile)
**Profile Location**: `C:\Users\shailmishra\OneDrive - Microsoft\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`

| Alias | Command | Description |
|-------|---------|-------------|
| `ghcs` | `gh copilot suggest` | Get command suggestions from Copilot |
| `ghce` | `gh copilot explain` | Get explanations for commands |

**Usage Examples**:
```powershell
ghcs "find large files in git history"
ghce "git rebase -i HEAD~3"
gh copilot suggest "list all docker containers"
gh copilot explain "awk '{print $1}' file.txt"
```

### GitHub Account Management Commands
```powershell
gh auth switch                    # Interactive switch between accounts
gh auth status                    # View all logged-in accounts
gh auth logout -h github.com      # Logout from GitHub
gh auth login                     # Login to a new account
```

---

## 2. Azure CLI Configuration

### Tool Versions (Validated ✅)
| Tool | Version | Status |
|------|---------|--------|
| Azure CLI | 2.80.0 | ✅ Installed |
| Azure ML Extension | 2.41.0 | ✅ Installed |
| Python (bundled) | 3.13.9 | ✅ Bundled |

**Installation Path**: `C:\Program Files\Microsoft SDKs\Azure\CLI2\`  
**Config Directory**: `C:\Users\shailmishra\.azure`  
**Extensions Directory**: `C:\Users\shailmishra\.azure\cliextensions`

### Azure Subscription (Validated ✅)
| Property | Value |
|----------|-------|
| **Subscription Name** | ME-MngEnvMCAP078213-shailmishra-1 |
| **Subscription ID** | `17107fe3-5d6d-4cfb-940a-8eeb0e651e2e` |
| **Tenant ID** | `2a06ed80-770f-4890-8cd8-77865656bc00` |
| **Tenant Name** | Contoso |
| **Tenant Domain** | MngEnvMCAP078213.onmicrosoft.com |
| **Cloud** | AzureCloud |
| **State** | ✅ Enabled |

### Registered Resource Providers (Validated ✅)
| Provider | Status | Purpose |
|----------|--------|---------|
| Microsoft.MachineLearningServices | ✅ Registered | AI Foundry, ML Workspaces |
| Microsoft.CognitiveServices | ✅ Registered | Azure OpenAI, AI Services |

### Common Azure CLI Commands
```powershell
az login                          # Login to Azure (browser)
az login --use-device-code        # Login with device code
az account show                   # Show current subscription
az account list --output table    # List all subscriptions
az account set --subscription <id>  # Switch subscription
az upgrade                        # Upgrade Azure CLI
```

---

## 3. Azure AI Foundry

### AI Hub (Validated ✅)
| Property | Value |
|----------|-------|
| **Hub Name** | ai-hub-shailesh |
| **Display Name** | Shailesh AI Hub |
| **Location** | West US 2 |
| **Resource Group** | new-res-grp-1 |
| **Public Network Access** | Disabled |
| **Data Isolation** | Enabled |
| **Provisioning State** | ✅ Succeeded |

**Hub Resource ID**:
```
/subscriptions/17107fe3-5d6d-4cfb-940a-8eeb0e651e2e/resourceGroups/new-res-grp-1/providers/Microsoft.MachineLearningServices/workspaces/ai-hub-shailesh
```

### Associated Hub Resources (Validated ✅)
| Resource Type | Name | Location |
|---------------|------|----------|
| Storage Account | aihubshastoragec137168a7 | West US 2 |
| Key Vault | aihubshakeyvault49d2184f | West US 2 |

### AI Project (Validated ✅)
| Property | Value |
|----------|-------|
| **Project Name** | ai-project-shailesh |
| **Display Name** | Shailesh AI Project |
| **Location** | West US 2 |
| **Resource Group** | new-res-grp-1 |
| **Parent Hub** | ai-hub-shailesh |
| **Provisioning State** | ✅ Succeeded |

**Project Resource ID**:
```
/subscriptions/17107fe3-5d6d-4cfb-940a-8eeb0e651e2e/resourceGroups/new-res-grp-1/providers/Microsoft.MachineLearningServices/workspaces/ai-project-shailesh
```

### AI Foundry Portal
**URL**: [https://ai.azure.com](https://ai.azure.com)

### AI Foundry CLI Commands
```powershell
# List all AI Hubs/Projects
az ml workspace list --resource-group "new-res-grp-1" --output table

# Show specific hub details
az ml workspace show --name "ai-hub-shailesh" --resource-group "new-res-grp-1"

# Show specific project details
az ml workspace show --name "ai-project-shailesh" --resource-group "new-res-grp-1"

# Create a new project within the hub
az ml workspace create `
  --name "new-project" `
  --resource-group "new-res-grp-1" `
  --location "westus2" `
  --kind project `
  --hub-id "/subscriptions/17107fe3-5d6d-4cfb-940a-8eeb0e651e2e/resourceGroups/new-res-grp-1/providers/Microsoft.MachineLearningServices/workspaces/ai-hub-shailesh"
```

---

## 4. Azure OpenAI Service

### Azure OpenAI Resource (Validated ✅)
| Property | Value |
|----------|-------|
| **Resource Name** | aoai-shailesh |
| **Location** | East US |
| **Resource Group** | new-res-grp-1 |
| **Kind** | OpenAI |
| **SKU** | S0 |
| **Endpoint** | https://aoai-shailesh-eastus.openai.azure.com/ |
| **Provisioning State** | ✅ Succeeded |
| **Local Auth** | Disabled (Azure AD only) |

### Model Deployments (Validated ✅)
| Deployment Name | Model | Version | SKU | Capacity | State |
|-----------------|-------|---------|-----|----------|-------|
| gpt-4o | gpt-4o | 2024-11-20 | GlobalStandard | 10K TPM | ✅ Succeeded |

### GPT-4o Model Capabilities
- ✅ Chat Completion
- ✅ Assistants API
- ✅ Agents V2
- ✅ JSON Schema Response
- ✅ Responses API

### Azure OpenAI Portals
- **Azure OpenAI Studio**: [https://oai.azure.com](https://oai.azure.com)
- **AI Foundry Portal**: [https://ai.azure.com](https://ai.azure.com)

### Azure OpenAI CLI Commands
```powershell
# List deployments
az cognitiveservices account deployment list `
  --name "aoai-shailesh" `
  --resource-group "new-res-grp-1" `
  -o table

# Create a new model deployment
az cognitiveservices account deployment create `
  --name "aoai-shailesh" `
  --resource-group "new-res-grp-1" `
  --deployment-name "gpt-4o-mini" `
  --model-name "gpt-4o-mini" `
  --model-version "2024-07-18" `
  --model-format "OpenAI" `
  --sku-capacity 10 `
  --sku-name "GlobalStandard"

# Delete a deployment
az cognitiveservices account deployment delete `
  --name "aoai-shailesh" `
  --resource-group "new-res-grp-1" `
  --deployment-name "<deployment-name>"

# Show resource details
az cognitiveservices account show `
  --name "aoai-shailesh" `
  --resource-group "new-res-grp-1"
```

### Using Azure OpenAI with Python
```python
# Install required packages:
# pip install openai azure-identity

from openai import AzureOpenAI
from azure.identity import DefaultAzureCredential, get_bearer_token_provider

# Azure AD Authentication (recommended - local auth is disabled)
token_provider = get_bearer_token_provider(
    DefaultAzureCredential(), 
    "https://cognitiveservices.azure.com/.default"
)

client = AzureOpenAI(
    azure_endpoint="https://aoai-shailesh-eastus.openai.azure.com/",
    azure_ad_token_provider=token_provider,
    api_version="2024-10-21"
)

response = client.chat.completions.create(
    model="gpt-4o",  # deployment name
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)
```

### Using Azure OpenAI with curl (PowerShell)
```powershell
# Get access token
$token = az account get-access-token --resource https://cognitiveservices.azure.com --query accessToken -o tsv

# Call the API
$body = @{
    messages = @(@{role = "user"; content = "Hello!"})
} | ConvertTo-Json

Invoke-RestMethod `
  -Uri "https://aoai-shailesh-eastus.openai.azure.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-10-21" `
  -Headers @{Authorization = "Bearer $token"; "Content-Type" = "application/json"} `
  -Method POST `
  -Body $body
```

---

## 5. All Azure Resources (Validated ✅)

### Resource Group: new-res-grp-1
| Resource Name | Type | Location | Status |
|---------------|------|----------|--------|
| ai-hub-shailesh | ML Workspace (Hub) | West US 2 | ✅ Succeeded |
| ai-project-shailesh | ML Workspace (Project) | West US 2 | ✅ Succeeded |
| aoai-shailesh | Cognitive Services (OpenAI) | East US | ✅ Succeeded |
| aihubshastoragec137168a7 | Storage Account | West US 2 | ✅ Active |
| aihubshakeyvault49d2184f | Key Vault | West US 2 | ✅ Active |

### All Resource Groups
| Name | Location | Status |
|------|----------|--------|
| Default-ActivityLogAlerts | East US | ✅ Succeeded |
| new-res-grp-1 | West US 2 | ✅ Succeeded |

---

## 6. Quick Reference

### Refresh PATH in PowerShell
```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### Reload PowerShell Profile
```powershell
. $PROFILE
```

### Key File Locations
| File | Path |
|------|------|
| Git Config | `C:\Users\shailmishra\.gitconfig` |
| Azure Config | `C:\Users\shailmishra\.azure` |
| PowerShell Profile | `C:\Users\shailmishra\OneDrive - Microsoft\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1` |

### Important URLs
| Service | URL |
|---------|-----|
| Azure AI Foundry | https://ai.azure.com |
| Azure OpenAI Studio | https://oai.azure.com |
| Azure Portal | https://portal.azure.com |
| GitHub | https://github.com |

---

## 7. Troubleshooting

### GitHub CLI Issues
```powershell
gh auth refresh                   # Refresh authentication token
gh auth login                     # Re-authenticate
gh extension upgrade gh-copilot   # Update Copilot extension
```

### Azure CLI Issues
```powershell
az account clear                  # Clear cached credentials
az login                          # Re-login
az upgrade                        # Update Azure CLI
az extension update --name ml     # Update ML extension
```

### Validation Commands
```powershell
# Validate all configurations at once
Write-Host "Git:" -ForegroundColor Cyan; git config --global --list
Write-Host "GitHub:" -ForegroundColor Cyan; gh auth status
Write-Host "Azure:" -ForegroundColor Cyan; az account show -o table
Write-Host "AI Foundry:" -ForegroundColor Cyan; az ml workspace list -g new-res-grp-1 -o table
Write-Host "OpenAI:" -ForegroundColor Cyan; az cognitiveservices account deployment list --name aoai-shailesh -g new-res-grp-1 -o table
```

---

## 8. Cost Management

### Monitor Azure Costs
```powershell
# View current spending (requires Cost Management Reader role)
az consumption usage list --start-date 2026-01-01 --end-date 2026-01-31 -o table
```

### Azure OpenAI Pricing (GlobalStandard - GPT-4o)
- **Input**: $2.50 per 1M tokens
- **Output**: $10.00 per 1M tokens
- **Current Capacity**: 10K TPM (tokens per minute)

### Delete Resources (if needed)
```powershell
# Delete AI Project
az ml workspace delete --name "ai-project-shailesh" --resource-group "new-res-grp-1" --yes

# Delete AI Hub (will delete associated resources)
az ml workspace delete --name "ai-hub-shailesh" --resource-group "new-res-grp-1" --yes --permanently-delete

# Delete OpenAI resource
az cognitiveservices account delete --name "aoai-shailesh" --resource-group "new-res-grp-1"

# Delete entire resource group
az group delete --name "new-res-grp-1" --yes
```

---

*Documentation generated and validated on January 16, 2026*
