# 📦 Hosting Setup Guide

Guide to host your trading bot installation scripts on GitHub.

## Prerequisites

- GitHub account
- Git installed locally
- Bot source files ready

## Step 1: Create GitHub Repositories

You need TWO repositories:

### Repository 1: Proxmox Installation Scripts

```bash
# Create on GitHub: trading-bot-proxmox
# Then locally:
mkdir trading-bot-proxmox
cd trading-bot-proxmox
git init

# Add files
cp /path/to/build.func .
cp /path/to/trading-bot-vm.sh .
cp /path/to/README.md .

# Create .gitignore
cat > .gitignore << EOF
*.log
.DS_Store
EOF

# Commit and push
git add .
git commit -m "Initial commit: Proxmox installation scripts"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/trading-bot-proxmox.git
git push -u origin main
```

### Repository 2: Bot Source Files

```bash
# Create on GitHub: trading-bot-files
# Then locally:
mkdir trading-bot-files
cd trading-bot-files
git init

# Add your bot files
cp /path/to/setup-internal.sh .
cp /path/to/data_loader.py .
cp /path/to/feature_engineering.py .
cp /path/to/environment.py .
cp /path/to/train.py .
cp /path/to/config.yaml .
cp /path/to/requirements.txt .

# Commit and push
git add .
git commit -m "Initial commit: Trading bot source files"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/trading-bot-files.git
git push -u origin main
```

## Step 2: Update URLs in Scripts

### Update trading-bot-vm.sh

Replace YOUR_USERNAME and YOUR_REPO:

```bash
# Line ~12
source <(curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-proxmox/main/build.func)

# Line ~28
REPO_URL="${REPO_URL:-https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-files/main}"
```

### Update setup-internal.sh

```bash
# Line ~20
REPO_URL="${REPO_URL:-https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-files/main}"
```

## Step 3: Test Installation

### From Proxmox Host

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-proxmox/main/trading-bot-vm.sh)"
```

### Inside VM

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-files/main/setup-internal.sh)
```

## Step 4: Make Repositories Public

1. Go to GitHub repository settings
2. Scroll to "Danger Zone"
3. Click "Change repository visibility"
4. Select "Public"
5. Confirm

## Step 5: Create Releases (Optional)

### Tag Versions

```bash
cd trading-bot-proxmox
git tag -a v1.0.0 -m "First stable release"
git push origin v1.0.0

cd ../trading-bot-files
git tag -a v1.0.0 -m "First stable release"
git push origin v1.0.0
```

### Use Versioned URLs

Users can then install specific versions:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-proxmox/v1.0.0/trading-bot-vm.sh)"
```

## Alternative: Self-Hosted

If you prefer not using GitHub:

### Option A: Your Own Web Server

```bash
# Upload to your server
scp build.func user@yourserver.com:/var/www/html/trading-bot/
scp trading-bot-vm.sh user@yourserver.com:/var/www/html/trading-bot/
scp setup-internal.sh user@yourserver.com:/var/www/html/trading-bot/

# Update URLs in scripts to:
# https://yourserver.com/trading-bot/build.func
# https://yourserver.com/trading-bot/setup-internal.sh
```

### Option B: Proxmox Host Itself

```bash
# On Proxmox, setup simple HTTP server
mkdir -p /var/www/trading-bot
cp *.sh /var/www/trading-bot/

# Install nginx
apt install nginx

# Configure nginx to serve files
cat > /etc/nginx/sites-available/trading-bot << 'EOF'
server {
    listen 8000;
    root /var/www/trading-bot;
    autoindex on;
}
EOF

ln -s /etc/nginx/sites-available/trading-bot /etc/nginx/sites-enabled/
systemctl restart nginx

# Now use:
bash -c "$(curl -fsSL http://proxmox-ip:8000/trading-bot-vm.sh)"
```

### Option C: GitLab/Gitea

Same process as GitHub but using your preferred Git platform.

## Security Considerations

### Private Repositories

If repositories are private:

```bash
# Use GitHub Personal Access Token
TOKEN=your_github_token

bash -c "$(curl -fsSL -H 'Authorization: token $TOKEN' https://raw.githubusercontent.com/...)"
```

### Script Verification

Users can verify script integrity:

```bash
# Download script first
curl -fsSL https://raw.githubusercontent.com/.../trading-bot-vm.sh -o trading-bot-vm.sh

# Review code
less trading-bot-vm.sh

# Run if satisfied
bash trading-bot-vm.sh
```

### Checksums

Provide SHA256 checksums:

```bash
sha256sum trading-bot-vm.sh > checksums.txt
sha256sum setup-internal.sh >> checksums.txt

# Users verify:
sha256sum -c checksums.txt
```

## Updates and Maintenance

### Update Scripts

```bash
# Edit files locally
vim trading-bot-vm.sh

# Commit and push
git add trading-bot-vm.sh
git commit -m "Update: improved error handling"
git push

# Create new release
git tag -a v1.1.0 -m "Version 1.1.0"
git push origin v1.1.0
```

### Notify Users

Create GitHub Release with changelog:

```markdown
## v1.1.0 - 2025-11-21

### Added
- GPU passthrough support
- Better error messages
- Automatic backup script

### Fixed
- Cloud-init timeout issue
- IP detection for some networks

### Changed
- Default memory from 8GB to 16GB
```

## Distribution

### Share Installation Command

Create simple documentation:

```markdown
# Trading Bot Installation

Run on your Proxmox host:
\`\`\`bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-proxmox/main/trading-bot-vm.sh)"
\`\`\`

That's it! 🚀
```

### QR Code (Optional)

Generate QR code for easy mobile sharing:

```bash
# Using qrencode
qrencode -t PNG -o install-qr.png "bash -c \"\$(curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-proxmox/main/trading-bot-vm.sh)\""
```

## Monitoring

### GitHub Stats

Track:
- Stars ⭐
- Forks 🍴
- Issues 🐛
- Pull Requests 🔄

### Usage Analytics (Optional)

Add simple analytics:

```bash
# In trading-bot-vm.sh, add at start:
curl -s "https://your-analytics.com/ping?source=github&version=1.0.0" > /dev/null || true
```

---

**Your scripts are now publicly available and ready to use!**
