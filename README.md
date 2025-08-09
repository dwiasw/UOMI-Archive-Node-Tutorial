# 🚀 Uomi Archive Node Setup Guide

> 🎉 **50,000,000 $UOMI** tokens have been allocated to reward node operators starting from this **testnet**!

---

## 🖥 Server Specifications

**Minimum Requirements**:
- **CPU**: 8 Cores
- **RAM**: 16GB
- **Storage**: 500GB SSD *(NVMe recommended)*

---

## 1️⃣ Install Prerequisites
```bash
sudo apt update && sudo apt upgrade -y && sudo apt autoremove && sudo apt-get install -y     curl     jq     build-essential     libssl-dev     pkg-config     cmake     git     libclang-dev && sudo ufw allow ssh && sudo ufw allow 9944/tcp && sudo ufw allow 30333/tcp
```

---

## 2️⃣ Prepare Environment
```bash
sudo useradd --no-create-home --shell /usr/sbin/nologin uomi && sudo mkdir -p /var/lib/uomi && sudo chown -R uomi:uomi /var/lib/uomi
```

---

## 3️⃣ Install & Configure Node

> Before you download make sure you update peers on 'genesis.json
> Get update peers at https://app.uomi.ai/peers

### 📥 Download & Update `genesis.json`
```bash
cd $HOME && mkdir uomi && cd $HOME/uomi && wget -O genesis.json https://github.com/Uomi-network/uomi-node/releases/latest/download/genesis.json && jq '.bootNodes = [Paste Peers In Here]' /usr/local/bin/genesis.json > tmp && sudo mv tmp /usr/local/bin/genesis.json
```

---

### 🐧 Install Binary

**For Ubuntu 22:**
```bash
wget -O uomi https://github.com/Uomi-network/uomi-node/releases/latest/download/uomi_ubuntu_22 && chmod +x uomi && sudo cp uomi /usr/local/bin/ && sudo chmod +x /usr/local/bin/uomi && sudo cp genesis.json /usr/local/bin/
```

**For Ubuntu 24:**
```bash
wget -O uomi https://github.com/Uomi-network/uomi-node/releases/latest/download/uomi_ubuntu_24 && chmod +x uomi && sudo cp uomi /usr/local/bin/ && sudo chmod +x /usr/local/bin/uomi && sudo cp genesis.json /usr/local/bin/
```
> 🟢 **Note:** Change `uomi_ubuntu_22` or `uomi_ubuntu_24` to `uomi`

---

### ⚙️ Create Systemd Service
```bash
sudo nano /etc/systemd/system/uomi.service
```
Paste the following:
```ini
[Unit]
Description=Uomi Node
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
User=uomi
Group=uomi
Restart=always
RestartSec=10
LimitNOFILE=65535
ExecStart=/usr/local/bin/uomi \
    --name "node-name" \
    --chain "/usr/local/bin/genesis.json" \
    --base-path "/var/lib/uomi" \
    --pruning archive \
    --rpc-cors all \
    --rpc-external \
    --rpc-methods Safe \
    --enable-evm-rpc \
    --prometheus-external \
    --telemetry-url "wss://telemetry.polkadot.io/submit/ 0"

# Hardening
ProtectSystem=strict
PrivateTmp=true
PrivateDevices=true
NoNewPrivileges=true
ReadWritePaths=/var/lib/uomi

[Install]
WantedBy=multi-user.target
```

> 🟢 **Note:** Change `node-namwe` to all you want eg `claudya`

---

## ▶️ Running Your Node
```bash
sudo systemctl daemon-reload && sudo systemctl enable uomi.service && sudo systemctl start uomi.service
```

Check status:
```bash
sudo systemctl status uomi.service --no-pager
```

Verify RPC endpoint:
```bash
curl -H "Content-Type: application/json"     -d '{"id":1, "jsonrpc":"2.0", "method": "system_health", "params":[]}'     http://localhost:9944
```

---

## 🛠 Useful Commands

- **Check logs**
```bash
journalctl -fu uomi.service
```

- **Stop node**
```bash
sudo systemctl stop uomi.service && sudo systemctl disable uomi.service
```

- **Restart node**
```bash
sudo systemctl restart uomi.service
```

- **Backup node**
```bash
sudo tar -czf uomi-backup-$(date +%Y%m%d).tar.gz /var/lib/uomi
```

- **Delete node**
```bash
sudo systemctl stop uomi.service && sudo systemctl disable uomi.service && sudo rm /etc/systemd/system/uomi.service && sudo systemctl daemon-reload && rm -rf /$HOME/uomi
```

---

## 📚 Additional Resources
- **Validator Nodes**: [Validator Requirements](https://docs.uomi.ai/build/run-a-node/become-a-validator/validator-requirements)
- **Official Docs**: [Run an Archive Node](https://docs.uomi.ai/build/run-a-node/run-an-archive-node/binary)
- **Twitter Source**: [UomiNetwork Post](https://x.com/UomiNetwork/status/1882845075887042857)
