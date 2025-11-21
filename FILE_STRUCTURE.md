# 📁 File Structure for GitHub Hosting

## Repository 1: trading-bot-proxmox

Your main installation repository:

```
trading-bot-proxmox/
│
├── README.md                 # Main documentation
├── LICENSE                   # MIT License
├── HOSTING_GUIDE.md          # This guide
│
├── build.func                # Helper functions library
├── trading-bot-vm.sh         # Main installation script
│
├── examples/
│   ├── custom-install.sh     # Custom installation example
│   └── multi-vm.sh           # Multiple VM deployment
│
├── docs/
│   ├── TROUBLESHOOTING.md    # Common issues
│   ├── CONFIGURATION.md      # Configuration guide
│   └── ADVANCED.md           # Advanced features
│
└── .github/
    └── workflows/
        └── test.yml          # CI/CD (optional)
```

## Repository 2: trading-bot-files

Your bot source code:

```
trading-bot-files/
│
├── README.md                 # Bot documentation
├── LICENSE                   # MIT License
│
├── setup-internal.sh         # Internal VM setup script
│
├── src/
│   ├── data_loader.py        # Data loading module
│   ├── feature_engineering.py # Feature engineering
│   ├── environment.py        # Trading environment
│   └── __init__.py
│
├── scripts/
│   ├── train.py              # Training script
│   ├── backtest.py           # Backtesting script
│   └── evaluate.py           # Evaluation script
│
├── config/
│   ├── config.yaml           # Main configuration
│   └── config.example.yaml   # Example config
│
├── requirements.txt          # Python dependencies
├── requirements-dev.txt      # Development dependencies
│
└── tests/
    ├── test_data_loader.py
    ├── test_features.py
    └── test_environment.py
```

## Installation Flow

```
User runs command on Proxmox
         ↓
Downloads trading-bot-vm.sh from Repo 1
         ↓
trading-bot-vm.sh sources build.func from Repo 1
         ↓
Creates VM, configures, starts
         ↓
User SSHs to VM
         ↓
Runs setup-internal.sh from Repo 2
         ↓
setup-internal.sh downloads all source files from Repo 2
         ↓
Bot installed and ready!
```

## File Descriptions

### build.func
- Helper functions for Proxmox operations
- Color output formatting
- Error handling
- VM creation utilities

### trading-bot-vm.sh
- Main installation entry point
- Reads configuration from environment
- Creates VM on Proxmox
- Configures cloud-init
- Starts VM and displays info

### setup-internal.sh
- Runs INSIDE the created VM
- Installs system dependencies
- Downloads bot source code
- Sets up Python environment
- Creates systemd service

### Source Files (data_loader.py, etc.)
- Your actual trading bot code
- Should be well-documented
- Include type hints
- Add docstrings

### requirements.txt
- All Python dependencies
- Pinned versions for stability
- Generated with: pip freeze > requirements.txt

### config.yaml
- Trading bot configuration
- Algorithm parameters
- Risk management settings
- Data sources

## Minimum Required Files

To get started, you MUST have:

**Repo 1 (trading-bot-proxmox):**
- ✅ build.func
- ✅ trading-bot-vm.sh
- ✅ README.md

**Repo 2 (trading-bot-files):**
- ✅ setup-internal.sh
- ✅ data_loader.py
- ✅ feature_engineering.py
- ✅ environment.py
- ✅ train.py
- ✅ config.yaml
- ✅ requirements.txt

## Optional But Recommended

- LICENSE file (MIT recommended)
- CHANGELOG.md (track changes)
- CONTRIBUTING.md (if accepting PRs)
- .gitignore (exclude logs, cache, etc.)
- Tests (pytest for source code)
- CI/CD (GitHub Actions for testing)

## Example .gitignore

```gitignore
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
venv/
env/

# Logs
*.log
logs/

# Data
data/raw/
data/processed/
*.csv
*.parquet

# Models
*.pkl
*.h5
*.pt
data/models/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Proxmox
*.qcow2
```

## Creating from Scratch

```bash
# 1. Create local directories
mkdir -p trading-bot-proxmox trading-bot-files

# 2. Copy your files
cp build.func trading-bot-vm.sh README.md trading-bot-proxmox/
cp setup-internal.sh *.py config.yaml requirements.txt trading-bot-files/

# 3. Initialize git
cd trading-bot-proxmox
git init
git add .
git commit -m "Initial commit"

cd ../trading-bot-files  
git init
git add .
git commit -m "Initial commit"

# 4. Create GitHub repos (via web interface)

# 5. Push
cd ../trading-bot-proxmox
git remote add origin https://github.com/YOU/trading-bot-proxmox.git
git push -u origin main

cd ../trading-bot-files
git remote add origin https://github.com/YOU/trading-bot-files.git
git push -u origin main
```

Done! 🎉
