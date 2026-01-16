# Development Environment Setup Guide - macOS

**Purpose**: Replicable setup guide for macOS development environment  
**Last Updated**: January 16, 2026  
**Target OS**: macOS 12 (Monterey) or later  

---

## Prerequisites

- macOS 12 (Monterey) or later
- Administrator access (ability to use `sudo`)
- Internet connection
- Terminal app (built-in) or iTerm2

---

## Step 0: Install Homebrew (Package Manager)

Homebrew is the package manager for macOS (equivalent to winget on Windows).

### 0.1 Install Homebrew
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 0.2 Add Homebrew to PATH (Apple Silicon Macs)
```bash
# For Apple Silicon (M1/M2/M3)
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"

# For Intel Macs (usually automatic)
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/usr/local/bin/brew shellenv)"
```

### 0.3 Verify Installation
```bash
brew --version
```

### 0.4 Update Homebrew
```bash
brew update
brew upgrade
```

---

## Step 1: Install Git

### 1.1 Install Git
```bash
brew install git
```

### 1.2 Verify Installation
```bash
git --version
```

### 1.3 Configure Git Global Settings
```bash
# Replace with your details
git config --global user.name "YOUR_GITHUB_USERNAME"
git config --global user.email "YOUR_EMAIL@users.noreply.github.com"
git config --global credential.helper osxkeychain
git config --global init.defaultBranch main
git config --global core.autocrlf input
```

### 1.4 Verify Configuration
```bash
git config --global --list
```

---

## Step 2: Install GitHub CLI

### 2.1 Install GitHub CLI
```bash
brew install gh
```

### 2.2 Verify Installation
```bash
gh --version
```

### 2.3 Authenticate with GitHub
```bash
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

### 2.4 Verify Authentication
```bash
gh auth status
```

---

## Step 3: Install GitHub Copilot CLI

### 3.1 Install Copilot Extension
```bash
gh extension install github/gh-copilot
```

### 3.2 Verify Installation
```bash
gh copilot --version
gh extension list
```

### 3.3 Add Copilot Aliases to Shell Profile

#### For Zsh (default on macOS)
```bash
# Add aliases to .zshrc
cat >> ~/.zshrc << 'EOF'

# GitHub Copilot CLI aliases
ghcs() {
    FUNCNAME="$funcstack[1]"
    TARGET="shell"
    local GH_DEBUG="$GH_DEBUG"
    local GH_HOST="$GH_HOST"

    read -r -d '' __USAGE <<-EOF
    Wrapper around \`gh copilot suggest\` to suggest a command based on a natural language description of the desired output effort.
    Supports executing suggested commands if applicable.

    USAGE
        $FUNCNAME [flags] <prompt>

    FLAGS
        -d, --debug              Enable debugging
        -h, --help               Display help usage
        -t, --target target      Target for suggestion; must be shell, gh, git
                                 default: "$TARGET"

    EXAMPLES
        - Guided experience
            $ $FUNCNAME

        - Git use cases
            $ $FUNCNAME -t git "Undo the most recent local commits"
            $ $FUNCNAME -t git "Clean up local branches"

        - GitHub CLI use cases
            $ $FUNCNAME -t gh "Create a new repo"
            $ $FUNCNAME -t gh "List open pull requests"

        - General use cases
            $ $FUNCNAME "Find large files in the git history"
            $ $FUNCNAME "List files by size"
EOF

    local OPT OPTARG OPTIND
    while getopts "dht:-:" OPT; do
        if [ "$OPT" = "-" ]; then
            OPT="${OPTARG%%=*}"
            OPTARG="${OPTARG#"$OPT"}"
            OPTARG="${OPTARG#=}"
        fi

        case "$OPT" in
            debug | d)
                naGH_DEBUG=api
                ;;

            help | h)
                echo "$__USAGE"
                return 0
                ;;

            target | t)
                TARGET="$OPTARG"
                ;;
        esac
    done

    shift $((OPTIND - 1))

    TMPFILE="$(mktemp -t gh-copilotXXXXXX)"
    trap 'rm -f "$TMPFILE"' EXIT
    if gh copilot suggest -t "$TARGET" "$*" --shell-out "$TMPFILE"; then
        if [ -s "$TMPFILE" ]; then
            FIXED_CMD="$(cat $TMPFILE)"
            print -s "$FIXED_CMD"
            echo
            eval "$FIXED_CMD"
        fi
    else
        return 1
    fi
}

ghce() {
    FUNCNAME="$funcstack[1]"
    local GH_DEBUG="$GH_DEBUG"
    local GH_HOST="$GH_HOST"

    read -r -d '' __USAGE <<-EOF
    Wrapper around \`gh copilot explain\` to explain a given input command in natural language.

    USAGE
        $FUNCNAME [flags] <command>

    FLAGS
        -d, --debug   Enable debugging
        -h, --help    Display help usage

    EXAMPLES
        $ $FUNCNAME "git rebase -i HEAD~3"
        $ $FUNCNAME "docker build -t my-image ."
EOF

    local OPT OPTARG OPTIND
    while getopts "dh-:" OPT; do
        if [ "$OPT" = "-" ]; then
            OPT="${OPTARG%%=*}"
            OPTARG="${OPTARG#"$OPT"}"
            OPTARG="${OPTARG#=}"
        fi

        case "$OPT" in
            debug | d)
                naGH_DEBUG=api
                ;;

            help | h)
                echo "$__USAGE"
                return 0
                ;;
        esac
    done

    shift $((OPTIND - 1))

    gh copilot explain "$*"
}
EOF

# Reload shell configuration
source ~/.zshrc
```

#### For Bash (if using bash)
```bash
# Add aliases to .bash_profile
cat >> ~/.bash_profile << 'EOF'

# GitHub Copilot CLI aliases
ghcs() {
    gh copilot suggest "$@"
}

ghce() {
    gh copilot explain "$@"
}
EOF

source ~/.bash_profile
```

### 3.4 Test Copilot CLI
```bash
ghcs "list all files in current directory"
ghce "git rebase -i HEAD~3"
```

---

## Step 4: Install Azure CLI

### 4.1 Install Azure CLI
```bash
brew install azure-cli
```

### 4.2 Verify Installation
```bash
az --version
```

### 4.3 Login to Azure

#### Option A: Browser Login (Interactive)
```bash
az login
```

#### Option B: Device Code (No Local Browser)
```bash
az login --use-device-code
```
1. Go to https://microsoft.com/devicelogin
2. Enter the code displayed
3. Sign in with your Azure account

#### Option C: Service Principal (Automation)
```bash
az login --service-principal -u <app-id> -p <password> --tenant <tenant-id>
```

### 4.4 Verify Login
```bash
az account show --output table
az account list --output table
```

### 4.5 Set Default Subscription (if multiple)
```bash
az account set --subscription "<subscription-name-or-id>"
```

---

## Step 5: Configure Azure Resource Providers

### 5.1 Register Required Providers
```bash
# For Azure AI Foundry
az provider register --namespace Microsoft.MachineLearningServices --wait

# For Azure OpenAI / Cognitive Services
az provider register --namespace Microsoft.CognitiveServices --wait
```

### 5.2 Verify Registration
```bash
az provider show --namespace Microsoft.MachineLearningServices --query "registrationState" -o tsv
az provider show --namespace Microsoft.CognitiveServices --query "registrationState" -o tsv
```

Both should return `Registered`.

---

## Step 6: Install Azure ML Extension

### 6.1 Install ML Extension
```bash
az extension add --name ml --yes
```

### 6.2 Verify Installation
```bash
az extension show --name ml --query "{Name:name, Version:version}" -o table
```

---

## Step 7: Create Azure AI Foundry Resources (Optional)

### 7.1 Create Resource Group
```bash
az group create --name "my-ai-rg" --location "westus2"
```

### 7.2 Create AI Hub
```bash
az ml workspace create \
  --name "my-ai-hub" \
  --resource-group "my-ai-rg" \
  --location "westus2" \
  --kind hub \
  --display-name "My AI Hub"
```

### 7.3 Get Hub Resource ID
```bash
HUB_ID=$(az ml workspace show --name "my-ai-hub" --resource-group "my-ai-rg" --query "id" -o tsv)
echo $HUB_ID
```

### 7.4 Create AI Project
```bash
az ml workspace create \
  --name "my-ai-project" \
  --resource-group "my-ai-rg" \
  --location "westus2" \
  --kind project \
  --hub-id "$HUB_ID" \
  --display-name "My AI Project"
```

### 7.5 Verify Resources
```bash
az ml workspace list --resource-group "my-ai-rg" --output table
```

---

## Step 8: Create Azure OpenAI Resource (Optional)

### 8.1 Create Azure OpenAI Resource
```bash
az cognitiveservices account create \
  --name "my-aoai" \
  --resource-group "my-ai-rg" \
  --location "eastus" \
  --kind "OpenAI" \
  --sku "S0" \
  --custom-domain "my-aoai-endpoint"
```

### 8.2 Deploy GPT-4o Model
```bash
az cognitiveservices account deployment create \
  --name "my-aoai" \
  --resource-group "my-ai-rg" \
  --deployment-name "gpt-4o" \
  --model-name "gpt-4o" \
  --model-version "2024-11-20" \
  --model-format "OpenAI" \
  --sku-capacity 10 \
  --sku-name "GlobalStandard"
```

### 8.3 Verify Deployment
```bash
az cognitiveservices account deployment list \
  --name "my-aoai" \
  --resource-group "my-ai-rg" \
  -o table
```

### 8.4 Get Endpoint
```bash
az cognitiveservices account show \
  --name "my-aoai" \
  --resource-group "my-ai-rg" \
  --query "properties.endpoint" -o tsv
```

---

## Step 9: Install .NET 6 SDK

### 9.1 Install .NET 6 SDK
```bash
brew install dotnet@6
```

### 9.2 Add to PATH (if needed)
```bash
echo 'export PATH="/opt/homebrew/opt/dotnet@6/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 9.3 Verify Installation
```bash
dotnet --list-sdks
```

---

## Step 10: Install .NET 8 SDK (LTS)

### 10.1 Install .NET 8 SDK
```bash
brew install dotnet@8

# Or install latest .NET SDK
brew install dotnet
```

### 10.2 Add to PATH (if needed)
```bash
echo 'export PATH="/opt/homebrew/opt/dotnet/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 10.3 Verify Installation
```bash
dotnet --version
dotnet --list-sdks
dotnet --list-runtimes
```

---

## Step 11: Install Python (Optional - for AI/ML)

### 11.1 Install Python
```bash
brew install python@3.12
```

### 11.2 Add to PATH (if needed)
```bash
echo 'export PATH="/opt/homebrew/opt/python@3.12/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 11.3 Verify Installation
```bash
python3 --version
pip3 --version
```

### 11.4 Install Azure AI Packages
```bash
pip3 install openai azure-identity azure-ai-ml
```

---

## Step 12: Install VS Code (Optional)

### 12.1 Install VS Code
```bash
brew install --cask visual-studio-code
```

### 12.2 Install CLI Command
Open VS Code, press `Cmd+Shift+P`, type "Shell Command", and select "Install 'code' command in PATH"

### 12.3 Verify Installation
```bash
code --version
```

---

## Validation Script

Save this as `validate-env.sh` and run with `bash validate-env.sh`:

```bash
#!/bin/bash

echo "========================================"
echo "  Development Environment Validation   "
echo "========================================"

echo ""
echo "[1/9] Homebrew"
brew --version 2>/dev/null || echo "  ❌ Not installed"

echo ""
echo "[2/9] Git"
git --version 2>/dev/null || echo "  ❌ Not installed"

echo ""
echo "[3/9] GitHub CLI"
gh --version 2>/dev/null | head -1 || echo "  ❌ Not installed"

echo ""
echo "[4/9] GitHub Copilot CLI"
gh copilot --version 2>/dev/null || echo "  ❌ Not installed"

echo ""
echo "[5/9] GitHub Auth Status"
gh auth status 2>&1 | head -5

echo ""
echo "[6/9] Azure CLI"
az --version 2>/dev/null | head -1 || echo "  ❌ Not installed"

echo ""
echo "[7/9] Azure Subscription"
az account show --query "{Name:name, State:state}" -o table 2>/dev/null || echo "  ❌ Not logged in"

echo ""
echo "[8/9] .NET SDKs"
dotnet --list-sdks 2>/dev/null || echo "  ❌ Not installed"

echo ""
echo "[9/9] Python"
python3 --version 2>/dev/null || echo "  ⚠️ Not installed (optional)"

echo ""
echo "========================================"
echo "  Validation Complete                  "
echo "========================================"
```

Make it executable and run:
```bash
chmod +x validate-env.sh
./validate-env.sh
```

---

## Quick Reference Commands

### Homebrew
```bash
brew update                      # Update Homebrew
brew upgrade                     # Upgrade all packages
brew list                        # List installed packages
brew search <package>            # Search for packages
brew info <package>              # Package info
brew uninstall <package>         # Uninstall package
```

### Git
```bash
git config --global --list       # View config
git clone <repo-url>             # Clone repository
git status                       # Check status
git add .                        # Stage all changes
git commit -m "message"          # Commit changes
git push                         # Push to remote
```

### GitHub CLI
```bash
gh auth status                   # Check auth status
gh auth switch                   # Switch accounts
gh repo create                   # Create new repo
gh repo clone owner/repo         # Clone repo
gh pr create                     # Create pull request
gh issue list                    # List issues
```

### GitHub Copilot CLI
```bash
ghcs "your question"             # Get command suggestions
ghce "command to explain"        # Explain a command
gh copilot suggest "question"    # Direct usage
gh copilot explain "command"     # Direct usage
```

### Azure CLI
```bash
az login                         # Login to Azure
az account show                  # Current subscription
az account list -o table         # All subscriptions
az group list -o table           # Resource groups
az resource list -g <rg> -o table  # Resources in group
```

### .NET CLI
```bash
dotnet new console -n MyApp      # New console app
dotnet new webapi -n MyApi       # New Web API
dotnet build                     # Build project
dotnet run                       # Run project
dotnet publish -c Release        # Publish for production
```

---

## macOS-Specific Tips

### Managing Multiple .NET Versions
```bash
# List installed versions
brew list --versions | grep dotnet

# Switch default version (create symlink)
brew unlink dotnet@6
brew link dotnet@8
```

### Keychain Access for Credentials
Git credentials are stored in macOS Keychain. To view/manage:
1. Open "Keychain Access" app
2. Search for "github.com" or "azure"
3. View or delete stored credentials

### Fix "Developer Cannot Be Verified" Errors
```bash
# If Homebrew apps are blocked
sudo spctl --master-disable  # Disable Gatekeeper (not recommended)
# OR right-click app > Open > Open anyway
```

### Apple Silicon (M1/M2/M3) Notes
- Homebrew installs to `/opt/homebrew` instead of `/usr/local`
- Some older tools may need Rosetta 2: `softwareupdate --install-rosetta`
- Use `arch -arm64` or `arch -x86_64` to run specific architecture

---

## Troubleshooting

### Homebrew Issues
```bash
brew doctor                      # Diagnose issues
brew cleanup                     # Remove old versions
brew update-reset                # Reset Homebrew
```

### PATH Issues
```bash
# Check current PATH
echo $PATH

# Reload shell configuration
source ~/.zshrc      # For Zsh
source ~/.bash_profile  # For Bash
```

### Azure CLI Login Issues
```bash
az account clear                 # Clear cached credentials
az login --use-device-code       # Use device code flow
```

### GitHub Auth Issues
```bash
gh auth logout                   # Logout
gh auth login                    # Re-login
gh auth refresh                  # Refresh token
```

### Permission Denied Errors
```bash
# Fix Homebrew permissions
sudo chown -R $(whoami) /opt/homebrew
```

---

## Uninstall Commands (If Needed)

```bash
brew uninstall git
brew uninstall gh
brew uninstall azure-cli
brew uninstall dotnet@6
brew uninstall dotnet@8
brew uninstall dotnet
brew uninstall python@3.12
brew uninstall --cask visual-studio-code

# Remove GitHub Copilot extension
gh extension remove gh-copilot

# Uninstall Homebrew completely
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
```

---

## Comparison: Windows vs macOS Commands

| Task | Windows (PowerShell) | macOS (Terminal) |
|------|---------------------|------------------|
| Package Manager | `winget install <pkg>` | `brew install <pkg>` |
| Refresh PATH | `$env:Path = [Environment]::...` | `source ~/.zshrc` |
| Shell Config | `$PROFILE` | `~/.zshrc` or `~/.bash_profile` |
| Git Credential | `credential.helper manager` | `credential.helper osxkeychain` |
| Line Endings | `core.autocrlf true` | `core.autocrlf input` |
| Home Directory | `$HOME` or `~` | `$HOME` or `~` |
| Admin/Sudo | Run as Administrator | `sudo <command>` |
| .NET Path | Auto-configured | May need manual PATH |

---

*Guide created on January 16, 2026*
