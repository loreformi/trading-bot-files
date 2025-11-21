# ⚡ Quick Start Guide - One-Command Installation

## 🎯 Obiettivo

Installare il bot di trading ML su Proxmox con **UN SOLO COMANDO**, esattamente come lo script Passbolt che hai visto.

## 📊 Confronto: Passbolt vs Trading Bot

| Feature | Passbolt Script | Trading Bot Script |
|---------|----------------|-------------------|
| **Tipo** | LXC Container | VM Completa |
| **Comando** | `bash -c "$(curl ...passbolt.sh)"` | `bash -c "$(curl ...trading-bot-vm.sh)"` |
| **Tempo Setup** | ~3 minuti | ~5 minuti |
| **Dipendenze** | Web server | Python ML stack |
| **Accesso** | Web UI (HTTPS) | SSH + TensorBoard |
| **GPU Support** | ❌ | ✅ |
| **Risorse** | 2 CPU, 2GB RAM | 8 CPU, 16GB RAM |

## 🚀 Installazione in 3 Step

### STEP 1: Su Proxmox Host

```bash
# SSH al tuo Proxmox
ssh root@proxmox-ip

# Esegui ONE-LINER
bash -c "$(curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-proxmox/main/trading-bot-vm.sh)"
```

**Output atteso:**
```
╔════════════════════════════════════════════════════════════════╗
║              GOLD-VIX RL TRADING BOT                          ║
║              Automated ML Trading System                       ║
╚════════════════════════════════════════════════════════════════╝

✓ Running on Proxmox VE
✓ VM ID 200 is available
✓ Storage local-lvm available
✓ Cloud image already exists
✓ VM created successfully
✓ Disk imported
✓ Disk attached
✓ Cloud-Init drive added
✓ Boot configured
✓ VM started

╔════════════════════════════════════════════════════════════════╗
║  Installation Completed Successfully!
╚════════════════════════════════════════════════════════════════╝

VM Information:
  ➜ VM ID:       200
  ➜ IP Address:  192.168.1.100
  ➜ Username:    tradingbot
  ➜ Password:    TradingBot2025!
```

### STEP 2: SSH alla VM

```bash
# Aspetta 60 secondi per cloud-init
sleep 60

# Connetti (usa IP mostrato nel passo 1)
ssh tradingbot@192.168.1.100
```

### STEP 3: Installa Bot (dentro la VM)

```bash
# ONE-LINER per setup interno
bash <(curl -fsSL https://raw.githubusercontent.com/YOUR_USERNAME/trading-bot-files/main/setup-internal.sh)
```

**Output atteso:**
```
╔════════════════════════════════════════════════════════════════╗
║        GOLD-VIX TRADING BOT - INTERNAL SETUP                  ║
║        Machine Learning Trading System                         ║
╚════════════════════════════════════════════════════════════════╝

[OK] System updated
[OK] Dependencies installed
[OK] Python 3.11 installed
[OK] Docker installed
[OK] QEMU Guest Agent installed
[OK] Firewall configured
[OK] Project structure created
[OK] Bot files downloaded
[OK] Python environment ready
[OK] Service created

╔════════════════════════════════════════════════════════════════╗
║  Setup Completed Successfully!
╚════════════════════════════════════════════════════════════════╝

Start Training Now:
  sudo systemctl start trading-bot.service
```

**Fatto!** Il bot è installato e configurato. 🎉

## 🎮 Gestione Bot

### Comandi Rapidi

```bash
# Start
sudo systemctl start trading-bot.service

# Stop
sudo systemctl stop trading-bot.service

# Status
sudo systemctl status trading-bot.service

# Logs real-time
tail -f ~/trading-bot/gold_vix_bot/logs/bot.log

# TensorBoard (nuovo terminale)
cd ~/trading-bot/gold_vix_bot
source venv/bin/activate
tensorboard --logdir=logs/tensorboard --host=0.0.0.0
```

### Script Helper

All'interno di `~/trading-bot/gold_vix_bot/`:

```bash
./start.sh      # Avvia bot
./stop.sh       # Ferma bot
./status.sh     # Controlla status
```

## 🔧 Personalizzazione

### Custom Resources

```bash
# Prima dell'installazione, imposta variabili:
VM_ID=250 \
VM_CORES=16 \
VM_MEMORY=32768 \
VM_DISK_SIZE=200G \
bash -c "$(curl -fsSL https://raw.githubusercontent.com/.../trading-bot-vm.sh)"
```

### Custom Credentials

```bash
CI_USER=mybot \
CI_PASSWORD=MySecurePass123! \
bash -c "$(curl -fsSL https://raw.githubusercontent.com/.../trading-bot-vm.sh)"
```

### Custom Storage

```bash
VM_STORAGE=my-zfs-pool \
bash -c "$(curl -fsSL https://raw.githubusercontent.com/.../trading-bot-vm.sh)"
```

## 📊 Monitoring

### TensorBoard

Dal tuo browser:
```
http://192.168.1.100:6006
```

### Logs

```bash
# Service logs
sudo journalctl -u trading-bot.service -f

# Application logs
tail -f ~/trading-bot/gold_vix_bot/logs/bot.log

# Error logs
tail -f ~/trading-bot/gold_vix_bot/logs/bot_error.log
```

### System Resources

```bash
# CPU e RAM
htop

# Disk
df -h

# GPU (se disponibile)
nvidia-smi
```

## 🔄 Update

### Update Bot

```bash
# Da Proxmox host
bash -c "$(curl -fsSL https://raw.githubusercontent.com/.../trading-bot-vm.sh)" install update

# O manualmente nella VM
cd ~/trading-bot/gold_vix_bot
source venv/bin/activate
pip install --upgrade -r requirements.txt
sudo systemctl restart trading-bot.service
```

## 🗑️ Uninstall

### Rimuovi VM Completamente

```bash
# Da Proxmox host (ATTENZIONE: cancella tutto!)
bash -c "$(curl -fsSL https://raw.githubusercontent.com/.../trading-bot-vm.sh)" install uninstall
```

## 🐛 Troubleshooting

### VM non ottiene IP

```bash
# Su Proxmox
qm guest cmd 200 network-get-interfaces

# O controlla web UI Proxmox
# Datacenter → VM 200 → Summary
```

### Service non parte

```bash
# Check errori
sudo journalctl -u trading-bot.service -n 50

# Test manuale
cd ~/trading-bot/gold_vix_bot
source venv/bin/activate
python scripts/train.py
```

### Out of Memory

```bash
# Aumenta RAM (da Proxmox)
qm set 200 --memory 32768
qm reboot 200
```

## 💡 Tips

1. **Cambia password di default!** Usa `CI_PASSWORD=...`
2. **Aggiungi SSH key** in `~/.ssh/id_rsa.pub` prima dell'installazione
3. **Fai snapshot** prima del training: `qm snapshot 200 pre-training`
4. **Monitora logs** per le prime 24 ore
5. **Backup periodici** dei modelli

## 📚 Documentazione Completa

- [README Completo](README.md)
- [Guida Hosting](HOSTING_GUIDE.md)
- [Struttura File](FILE_STRUCTURE.md)

## 🆘 Supporto

Se hai problemi:
1. Check logs: `sudo journalctl -u trading-bot.service -n 100`
2. Test manuale: `python scripts/train.py`
3. Verifica risorse: `htop`, `df -h`, `free -h`
4. Apri issue su GitHub

---

**Tempo totale installazione:** ~10 minuti ⏱️  
**Difficoltà:** Facile ⭐  
**Requisiti conoscenze:** Comandi Linux base 📖
