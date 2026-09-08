---
title: Deploy Bun App on Debian VPS with Systemd
description: A concise guide to deploying a Bun application on a Debian-based VPS using systemd for process management.
pubDate: 2026-09-08
categories: [Deployment, Bun, Linux]
---

### 1. Install Bun
```bash
curl -fsSL https://bun.sh/install | bash
source ~/.bashrc
```

### 2. Setup App
```bash
git clone <repo_url> /var/www/my-app
cd /var/www/my-app
bun install --production
```

### 3. Systemd Service
Create `/etc/systemd/system/my-app.service`:

```ini
[Unit]
Description=Bun App
After=network.target

[Service]
Type=simple
User=www-data
WorkingDirectory=/var/www/my-app
ExecStart=/home/<user>/.bun/bin/bun run src/index.ts
Restart=always
Environment=NODE_ENV=production
Environment=PORT=3000

[Install]
WantedBy=multi-user.target
```

### 4. Enable & Start
```bash
sudo systemctl daemon-reload
sudo systemctl enable my-app
sudo systemctl start my-app
```

### 5. Monitoring Logs
To monitor the app logs in real-time:
```bash
journalctl -u my-app -f
```
