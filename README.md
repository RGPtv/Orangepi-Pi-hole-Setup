![Pi-hole](https://pi-hole.net/logo.svg)
# OrangePi Pi-hole Setup Guide 🍊

A streamlined guide to install **Pi-hole** on your Orange Pi, with both native and Docker-based options. Includes an automated fix for DNS resolution hiccups after reboot.

> Tested on **OrangePi Zero 3** running Debian/Ubuntu-based distributions.

---

## 📋 Table of Contents

1. [Requirements](#-requirements)
2. [Option 1: Native Installation](#-option-1-native-installation)
   - [Automated One-Liner](#automated-one-liner)
   - [Post-Install: DNS Fix on Reboot](#post-install-dns-fix-on-reboot)
3. [Option 2: Docker Installation](#-option-2-docker-installation)
   1. [Prepare Project Directory](#1-prepare-project-directory)
   2. [Configure Compose File](#2-configure-compose-file)
   3. [Launch Container](#3-launch-container)
   4. [Enable Auto-Start with systemd](#4-enable-auto-start-with-systemd)
4. [Testing & Validation](#-testing--validation)
5. [Troubleshooting](#-troubleshooting)
6. [Credits & License](#-credits--license)

---

## 📦 Requirements

- Orange Pi running a Debian/Ubuntu-based OS
- `root` or `sudo` privileges
- Active Internet connection
- *(Optional)* Docker & Docker Compose

---

## ✅ Option 1: Native Installation

### 🔰 Automated One-Liner

Install Pi-hole in one step using the official installer:

```bash
curl -sSL https://install.pi-hole.net | bash
```

1. Follow the interactive prompts.
2. Choose your upstream DNS provider, blocking lists, and interface.

### 🛠️ Post-Install: DNS Fix on Reboot

1. Download the DNS-restart script:
   ```bash
   wget \
     https://raw.githubusercontent.com/RGPtv/Orangepi-Pi-hole-Setup/main/restart-dns.sh \
     -O /usr/local/bin/restart-dns.sh
   chmod +x /usr/local/bin/restart-dns.sh
   ```
2. Schedule at boot:
   ```bash
   (sudo crontab -l 2>/dev/null; echo "@reboot sleep 5 && /usr/local/bin/restart-dns.sh") | sudo crontab -
   ```

> **Note:** `sleep 5` ensures system services are fully up before restarting DNS.

---

## 🐳 Option 2: Docker Installation

Run Pi-hole in a container for isolation and portability.

### 1. Prepare Project Directory

```bash
sudo mkdir -p /docker/pihole
sudo chown $USER:$USER /docker/pihole
cd /docker/pihole
```

### 2. Configure Compose File

```bash
wget https://raw.githubusercontent.com/RGPtv/Orangepi-Pi-hole-Setup/main/docker-compose.yml
nano docker-compose.yml
```

> Update network settings, ports, environment variables, and the admin password.

### 3. Launch Container

```bash
docker compose up -d
```

Verify with:
```bash
docker ps | grep pihole
```

### 4. Enable Auto-Start with systemd

Create `/etc/systemd/system/pihole.service`:

```ini
[Unit]
Description=Pi-hole Container
Requires=docker.service
After=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/docker/pihole
ExecStart=/usr/bin/docker compose up -d
ExecStop=/usr/bin/docker compose down
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable pihole.service
sudo systemctl start pihole.service
sudo systemctl status pihole.service
```

---

## 🧪 Testing & Validation

1. **Web UI**: Access `http://<OrangePi_IP>/admin/`
2. **Blocking Test**: Visit `http://pi.hole/admin/query` for query logs.
3. **Reboot Check**: Reboot and ensure DNS queries still resolve.

---

## ⚠️ Troubleshooting

- **No DNS after reboot**: Verify `restart-dns.sh` path and cron entry.
- **Docker port conflicts**: Change host ports in `docker-compose.yml`.
- **Permissions issues**: Ensure `/docker/pihole` is owned by the Docker user or service.

---

## 🙌 Credits & License

- **Guide Maintainer**: [RGPtv](https://github.com/RGPtv)
- **Pi-hole**: © Pi-hole LLC — licensed under [GPLv3](https://opensource.org/licenses/GPL-3.0)

---

*Happy ad-blocking!*
