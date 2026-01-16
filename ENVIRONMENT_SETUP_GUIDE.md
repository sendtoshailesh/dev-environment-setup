# Development Environment Setup Guide

**Purpose**: Replicable setup guide for Windows development environment  
**Last Updated**: January 16, 2026  
**Target OS**: Windows 10/11  

---

## Prerequisites

- Windows 10 version 1809 or later / Windows 11
- Administrator access
- Internet connection
- winget (Windows Package Manager) - comes pre-installed on Windows 11

### Verify winget is installed
```powershell
winget --version
```

If not installed, get it from the Microsoft Store (App Installer).

---

## Step 1: Install Git

### 1.1 Install Git
```powershell
winget install Git.Git --accept-source-agreements --accept-package-agreements --silent
```

### 1.2 Refresh PATH
```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### 1.3 Verify Installation
```powershell
git --version
```

### 1.4 Configure Git Global Settings
```powershell
# Replace with your details
git config --global user.name "YOUR_GITHUB_USERNAME"
git config --global user.email "YOUR_EMAIL@users.noreply.github.com"
git config --global credential.helper manager
git config --global init.defaultBranch main
git config --global core.autocrlf true
```

### 1.5 Verify Configuration
```powershell
git config --global --list
```

---

## Step 2: Install GitHub CLI

### 2.1 Install GitHub CLI
```powershell
winget install GitHub.cli --accept-source-agreements --accept-package-agreements --silent
```

### 2.2 Refresh PATH
```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### 2.3 Verify Installation
```powershell
gh --version
```

### 2.4 Authenticate with GitHub
```powershell
# Interactive login (opens browser)
gh auth login -h github.com

# OR use device code (no local browser needed)
gh auth login --web
```

Follow the prompts:
1. Select **HTTPS** as protocol
2. Choose **Yes** to authenticate Git with GitHub credentials
3. Choose **Login with a web browser**
4. Copy the code, open browser, paste code, and authorize

### 2.5 Verify Authentication
```powershell
gh auth status
```

---

## Step 3: Install GitHub Copilot CLI

### 3.1 Install Copilot Extension
```powershell
gh extension install github/gh-copilot
```

### 3.2 Verify Installation
```powershell
gh copilot --version
gh extension list
```

### 3.3 Add Copilot Aliases to PowerShell Profile

#### 3.3.1 Create/Open PowerShell Profile
```powershell
if (!(Test-Path -Path $PROFILE)) { New-Item -ItemType File -Path $PROFILE -Force }
notepad $PROFILE
```

#### 3.3.2 Add This Content to Profile
```powershell
# GitHub Copilot CLI aliases
function ghcs {
    param(
        [Parameter()]
        [string]$Hostname,
        [ValidateSet('gh', 'git', 'shell')]
        [Alias('t')]
        [String]$Target = 'shell',
        [Parameter(Position=0, ValueFromRemainingArguments)]
        [string]$Prompt
    )
    begin {
        $executeCommandFile = New-TemporaryFile
        $envGhDebug = $Env:GH_DEBUG
        $envGhHost = $Env:GH_HOST
    }
    process {
        if ($PSBoundParameters['Debug']) { $Env:GH_DEBUG = 'api' }
        $Env:GH_HOST = $Hostname
        gh copilot suggest -t $Target -s "$executeCommandFile" $Prompt
    }
    end {
        if ($executeCommandFile.Length -gt 0) {
            $executeCommand = (Get-Content -Path $executeCommandFile -Raw).Trim()
            [Microsoft.PowerShell.PSConsoleReadLine]::AddToHistory($executeCommand)
            $now = Get-Date
            $executeCommandHistoryItem = [PSCustomObject]@{
                CommandLine = $executeCommand
                ExecutionStatus = [Management.Automation.Runspaces.PipelineState]::NotStarted
                StartExecutionTime = $now
                EndExecutionTime = $now.AddSeconds(1)
            }
            Add-History -InputObject $executeCommandHistoryItem
            Write-Host "`n"
            Invoke-Expression $executeCommand
        }
    }
    clean {
        Remove-Item -Path $executeCommandFile
        $Env:GH_DEBUG = $envGhDebug
    }
}

function ghce {
    param(
        [Parameter()]
        [string]$Hostname,
        [Parameter(Position=0, ValueFromRemainingArguments)]
        [string[]]$Prompt
    )
    begin {
        $envGhDebug = $Env:GH_DEBUG
        $envGhHost = $Env:GH_HOST
    }
    process {
        if ($PSBoundParameters['Debug']) { $Env:GH_DEBUG = 'api' }
        $Env:GH_HOST = $Hostname
        gh copilot explain $Prompt
    }
    clean {
        $Env:GH_DEBUG = $envGhDebug
        $Env:GH_HOST = $envGhHost
    }
}
```

#### 3.3.3 Reload Profile
```powershell
. $PROFILE
```

### 3.4 Test Copilot CLI
```powershell
ghcs "list all files in current directory"
ghce "git rebase -i HEAD~3"
```

---

## Step 4: Install Azure CLI

### 4.1 Install Azure CLI
```powershell
winget install Microsoft.AzureCLI --accept-source-agreements --accept-package-agreements --silent
```

### 4.2 Refresh PATH
```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### 4.3 Verify Installation
```powershell
az --version
```

### 4.4 Login to Azure

#### Option A: Browser Login (Interactive)
```powershell
az login
```

#### Option B: Device Code (No Local Browser)
```powershell
az login --use-device-code
```
1. Go to https://microsoft.com/devicelogin
2. Enter the code displayed
3. Sign in with your Azure account

#### Option C: Service Principal (Automation)
```powershell
az login --service-principal -u <app-id> -p <password> --tenant <tenant-id>
```

### 4.5 Verify Login
```powershell
az account show --output table
az account list --output table
```

### 4.6 Set Default Subscription (if multiple)
```powershell
az account set --subscription "<subscription-name-or-id>"
```

---

## Step 5: Configure Azure Resource Providers

### 5.1 Register Required Providers
```powershell
# For Azure AI Foundry
az provider register --namespace Microsoft.MachineLearningServices --wait

# For Azure OpenAI / Cognitive Services
az provider register --namespace Microsoft.CognitiveServices --wait
```

### 5.2 Verify Registration
```powershell
az provider show --namespace Microsoft.MachineLearningServices --query "registrationState" -o tsv
az provider show --namespace Microsoft.CognitiveServices --query "registrationState" -o tsv
```

Both should return `Registered`.

---

## Step 6: Install Azure ML Extension

### 6.1 Install ML Extension
```powershell
az extension add --name ml --yes
```

### 6.2 Verify Installation
```powershell
az extension show --name ml --query "{Name:name, Version:version}" -o table
```

---

## Step 7: Create Azure AI Foundry Resources (Optional)

### 7.1 Create Resource Group
```powershell
az group create --name "my-ai-rg" --location "westus2"
```

### 7.2 Create AI Hub
```powershell
az ml workspace create `
  --name "my-ai-hub" `
  --resource-group "my-ai-rg" `
  --location "westus2" `
  --kind hub `
  --display-name "My AI Hub"
```

### 7.3 Get Hub Resource ID
```powershell
$hubId = az ml workspace show --name "my-ai-hub" --resource-group "my-ai-rg" --query "id" -o tsv
Write-Host $hubId
```

### 7.4 Create AI Project
```powershell
az ml workspace create `
  --name "my-ai-project" `
  --resource-group "my-ai-rg" `
  --location "westus2" `
  --kind project `
  --hub-id $hubId `
  --display-name "My AI Project"
```

### 7.5 Verify Resources
```powershell
az ml workspace list --resource-group "my-ai-rg" --output table
```

---

## Step 8: Create Azure OpenAI Resource (Optional)

### 8.1 Create Azure OpenAI Resource
```powershell
az cognitiveservices account create `
  --name "my-aoai" `
  --resource-group "my-ai-rg" `
  --location "eastus" `
  --kind "OpenAI" `
  --sku "S0" `
  --custom-domain "my-aoai-endpoint"
```

### 8.2 Deploy GPT-4o Model
```powershell
az cognitiveservices account deployment create `
  --name "my-aoai" `
  --resource-group "my-ai-rg" `
  --deployment-name "gpt-4o" `
  --model-name "gpt-4o" `
  --model-version "2024-11-20" `
  --model-format "OpenAI" `
  --sku-capacity 10 `
  --sku-name "GlobalStandard"
```

### 8.3 Verify Deployment
```powershell
az cognitiveservices account deployment list `
  --name "my-aoai" `
  --resource-group "my-ai-rg" `
  -o table
```

### 8.4 Get Endpoint
```powershell
az cognitiveservices account show `
  --name "my-aoai" `
  --resource-group "my-ai-rg" `
  --query "properties.endpoint" -o tsv
```

---

## Step 9: Install .NET 6 SDK

### 9.1 Install .NET 6 SDK
```powershell
winget install Microsoft.DotNet.SDK.6 --accept-source-agreements --accept-package-agreements --silent
```

### 9.2 Refresh PATH
```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### 9.3 Verify Installation
```powershell
dotnet --list-sdks
```

---

## Step 10: Install .NET 8 SDK (LTS)

### 10.1 Install .NET 8 SDK
```powershell
winget install Microsoft.DotNet.SDK.8 --accept-source-agreements --accept-package-agreements --silent
```

### 10.2 Refresh PATH
```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### 10.3 Verify Installation
```powershell
dotnet --version
dotnet --list-sdks
dotnet --list-runtimes
```

---

## Step 11: Install Python (Optional - for AI/ML)

### 11.1 Install Python
```powershell
winget install Python.Python.3.12 --accept-source-agreements --accept-package-agreements --silent
```

### 11.2 Refresh PATH
```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### 11.3 Verify Installation
```powershell
python --version
pip --version
```

### 11.4 Install Azure AI Packages
```powershell
pip install openai azure-identity azure-ai-ml
```

---

## Validation Script

Run this script to validate the entire setup:

```powershell
# Complete Environment Validation Script
Write-Host "========================================" -ForegroundColor Cyan
Write-Host "  Development Environment Validation   " -ForegroundColor Cyan
Write-Host "========================================" -ForegroundColor Cyan

# Refresh PATH
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")

Write-Host "`n[1/8] Git" -ForegroundColor Yellow
try { git --version } catch { Write-Host "  ❌ Not installed" -ForegroundColor Red }

Write-Host "`n[2/8] GitHub CLI" -ForegroundColor Yellow
try { gh --version | Select-Object -First 1 } catch { Write-Host "  ❌ Not installed" -ForegroundColor Red }

Write-Host "`n[3/8] GitHub Copilot CLI" -ForegroundColor Yellow
try { gh copilot --version } catch { Write-Host "  ❌ Not installed" -ForegroundColor Red }

Write-Host "`n[4/8] GitHub Auth Status" -ForegroundColor Yellow
gh auth status 2>&1 | Select-Object -First 5

Write-Host "`n[5/8] Azure CLI" -ForegroundColor Yellow
try { az --version | Select-Object -First 1 } catch { Write-Host "  ❌ Not installed" -ForegroundColor Red }

Write-Host "`n[6/8] Azure Subscription" -ForegroundColor Yellow
az account show --query "{Name:name, State:state}" -o table 2>&1

Write-Host "`n[7/8] .NET SDKs" -ForegroundColor Yellow
try { dotnet --list-sdks } catch { Write-Host "  ❌ Not installed" -ForegroundColor Red }

Write-Host "`n[8/8] Python" -ForegroundColor Yellow
try { python --version } catch { Write-Host "  ⚠️ Not installed (optional)" -ForegroundColor Yellow }

Write-Host "`n========================================" -ForegroundColor Cyan
Write-Host "  Validation Complete                  " -ForegroundColor Cyan
Write-Host "========================================" -ForegroundColor Cyan
```

---

## Quick Reference Commands

### Git
```powershell
git config --global --list       # View config
git clone <repo-url>             # Clone repository
git status                       # Check status
git add .                        # Stage all changes
git commit -m "message"          # Commit changes
git push                         # Push to remote
```

### GitHub CLI
```powershell
gh auth status                   # Check auth status
gh auth switch                   # Switch accounts
gh repo create                   # Create new repo
gh repo clone owner/repo         # Clone repo
gh pr create                     # Create pull request
gh issue list                    # List issues
```

### GitHub Copilot CLI
```powershell
ghcs "your question"             # Get command suggestions
ghce "command to explain"        # Explain a command
gh copilot suggest "question"    # Direct usage
gh copilot explain "command"     # Direct usage
```

### Azure CLI
```powershell
az login                         # Login to Azure
az account show                  # Current subscription
az account list -o table         # All subscriptions
az group list -o table           # Resource groups
az resource list -g <rg> -o table  # Resources in group
```

### .NET CLI
```powershell
dotnet new console -n MyApp      # New console app
dotnet new webapi -n MyApi       # New Web API
dotnet build                     # Build project
dotnet run                       # Run project
dotnet publish -c Release        # Publish for production
```

---

## Troubleshooting

### PATH Not Updated
```powershell
# Refresh PATH without restarting terminal
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### Azure CLI Login Issues
```powershell
az account clear                 # Clear cached credentials
az login --use-device-code       # Use device code flow
```

### GitHub Auth Issues
```powershell
gh auth logout                   # Logout
gh auth login                    # Re-login
gh auth refresh                  # Refresh token
```

### winget Not Found
Download "App Installer" from Microsoft Store, or install manually:
```powershell
Invoke-WebRequest -Uri https://aka.ms/getwinget -OutFile winget.msixbundle
Add-AppxPackage winget.msixbundle
```

---

## Uninstall Commands (If Needed)

```powershell
winget uninstall Git.Git
winget uninstall GitHub.cli
winget uninstall Microsoft.AzureCLI
winget uninstall Microsoft.DotNet.SDK.6
winget uninstall Microsoft.DotNet.SDK.8
winget uninstall Python.Python.3.12
```

---

*Guide created on January 16, 2026*
