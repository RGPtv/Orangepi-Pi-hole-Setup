# 🍊 Pi-hole Setup Guide for Orange Pi

This guide walks you through installing [Pi-hole](https://pi-hole.net/) on an Orange Pi, using either a direct installation or Docker. It also includes an automated fix for DNS resolution issues after reboot.

📅 **Tested on OrangePi Zero 3**

---

## 📦 Requirements

* Orange Pi running Debian/Ubuntu-based OS
* Root or sudo access
* Internet connection
* (Optional) Docker & Docker Compose

---

## ✅ Option 1: Install Pi-hole (One-Step Script)

Run the following command to install Pi-hole using the official interactive script:

```bash
curl -sSL https://install.pi-hole.net | bash
```

> 💡 This script will guide you through network configuration and web admin setup.

---

## 🔧 Fix DNS Issues on Reboot (For "Option 1" ONLY)

If DNS stops working after reboot, fix it with a helper script:

### 1. Download and set permissions:

```bash
wget https://raw.githubusercontent.com/RGPtv/Orangepi-Pi-hole-Setup/main/restart-dns.sh -O /usr/local/bin/restart-dns.sh
chmod +x /usr/local/bin/restart-dns.sh
```

### 2. Run at boot via `crontab`:

```bash
crontab -e
```

Add this line at the end:

```bash
@reboot sleep 5 && /usr/local/bin/restart-dns.sh
```

> ⏱️ The `sleep 5` ensures the system finishes booting before restarting DNS.

### 3. Test the script (Optional):

```bash
/usr/local/bin/restart-dns.sh
sudo reboot
```

---

## 🐳 Option 2: Install Pi-hole with Docker

For a portable and isolated setup using Docker.

### 1. Create project directory:

```bash
mkdir -p /docker/pihole
cd /docker/pihole
```

### 2. Download and edit `docker-compose.yml`:

```bash
wget https://raw.githubusercontent.com/RGPtv/Orangepi-Pi-hole-Setup/main/docker-compose.yml
nano docker-compose.yml
```

> ⚙️ Adjust the environment variables (password, IP, timezone, and etc) to fit your setup.

### 3. Start the container:

```bash
docker compose up -d
```

Pi-hole will now be running in the background.

### 4. Create a systemd service:

```bash
sudo nano /etc/systemd/system/pihole.service
```

Paste the following:

```ini
[Unit]
Description=Pi-hole Docker Service
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

### 5. Enable the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable pihole.service
sudo systemctl start pihole.service
sudo systemctl status pihole.service
```

---

Created by [RGPtv](https://github.com/RGPtv)

---
