# 🚀 Uomi Archive Node Setup Guide

> **Update**:  
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

### 📥 Download & Update `genesis.json`
```bash
cd $HOME && mkdir uomi && cd $HOME/uomi && wget -O genesis.json https://github.com/Uomi-network/uomi-node/releases/latest/download/genesis.json && jq '.bootNodes = [
  "/ip4/140.245.100.68/tcp/30333/ws/p2p/12D3KooWA7MnHnFkFjkon6Gr7QekARkDwMYCV9F1GBQnzAEviJ9K",
  "/ip4/135.181.254.83/tcp/30333/p2p/12D3KooWAPL9NeVH3hmCxdumjnBLba5RufSzv7b6ug5hDzMB5BPQ",
  "/ip4/193.253.183.22/tcp/30333/p2p/12D3KooWARwm8Rqxj7BHqyTcWVEMTX35YaCPN7eCc7HgxViKBHK9",
  "/ip4/142.132.171.138/tcp/30333/ws/p2p/12D3KooWBmMjDCouXfsdYSHLDQMWBDzz44AgRZxnD7SCy6rBzzNz",
  "/ip4/5.9.87.231/tcp/30333/ws/p2p/12D3KooWCAChuDHHpNER855r6NpAGDeLrSZ4U3sP6FocSWWJZDf5",
  "/ip4/95.214.55.87/tcp/30333/p2p/12D3KooWE7yggjMVV7KPRxpogGk1ATA3QZp5nPGDtKuFVNVcaeK3",
  "/ip4/176.187.197.224/tcp/30333/p2p/12D3KooWFMSANaDiVgSDn7kRbEd8tz5fAXKkG8hEmGEyKYSY7eY6",
  "/ip4/38.143.58.87/tcp/30333/ws/p2p/12D3KooWFYA2WsVVmYvv8pHnSW2SsPiwK1ombSEUahxyUhEJmA7x",
  "/ip4/157.90.5.250/tcp/30933/p2p/12D3KooWJDT4hpQcdH6W9Cdt3rLM1vWcVF96H1umvvNwYRiiMn4Q",
  "/ip4/116.203.89.159/tcp/30333/ws/p2p/12D3KooWJPh1Dn7tEzndBiBnEV3Vc4m4B4Kdu9bCzoxGptMCdLjR",
  "/ip4/84.208.197.38/tcp/30333/p2p/12D3KooWMAbFpa8BCej4t3YvdCfWyJvXzhrEYTzXg22CuMcKhgV5",
  "/ip4/195.3.223.174/tcp/30333/p2p/12D3KooWMpz3LEAeRA94LK9wH6NtEzyw1Z6KYUntjz7QwPe3rYNT",
  "/ip4/149.50.96.58/tcp/30333/p2p/12D3KooWPD2kra9CMWMQ7Y8jZbQeFAfdkGJ3LYZLD82V4E4HqYgc",
  "/ip4/65.109.142.4/tcp/30333/p2p/12D3KooWSGp485hkoGXUucjWRDKf9AXaYQy9aLzMWpqhPFw816rZ",
  "/ip4/37.27.247.51/tcp/30333/p2p/12D3KooWSkeGtChmcJWzsp9Wpys3T34KpGxdx7TzjMvioZ8UxMw7"
]' /usr/local/bin/genesis.json > tmp && sudo mv tmp /usr/local/bin/genesis.json
```

---

### 🐧 Install Binary

**For Ubuntu 22:**
```bash
wget -O uomi https://github.com/Uomi-network/uomi-node/releases/latest/download/uomi_ubuntu_22 && chmod +x uomi && sudo cp ./uomi /usr/local/bin/ && sudo chmod +x /usr/local/bin/uomi && sudo cp ./genesis.json /usr/local/bin/
```

**For Ubuntu 24:**
```bash
wget -O uomi https://github.com/Uomi-network/uomi-node/releases/latest/download/uomi_ubuntu_24 && chmod +x uomi && sudo cp ./uomi /usr/local/bin/ && sudo chmod +x /usr/local/bin/uomi && sudo cp ./genesis.json /usr/local/bin/
```

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
ExecStart=/usr/local/bin/uomi     --name "EDIT-WITH-YOUR-NODES-ARCHIVE-NAME"     --chain "/usr/local/bin/genesis.json"     --base-path "/var/lib/uomi"     --pruning archive     --rpc-cors all     --rpc-external     --rpc-methods Safe     --enable-evm-rpc     --prometheus-external     --telemetry-url "wss://telemetry.polkadot.io/submit/ 0"

# Hardening
ProtectSystem=strict
PrivateTmp=true
PrivateDevices=true
NoNewPrivileges=true
ReadWritePaths=/var/lib/uomi

[Install]
WantedBy=multi-user.target
```
> 🟢 **Note:** Change `EDIT-WITH-YOUR-NODES-ARCHIVE-NAME` to your own archive node name (e.g., `BUDI-NANGIS`)

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
