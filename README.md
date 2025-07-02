# Pi-hole Setup Guide for 🍊 OrangePi

A simple guide to install [Pi-hole](https://pi-hole.net/) on an Orange Pi, with support for both standard and Docker-based installations. It includes an automatic fix for DNS resolution issues after reboot.

✅ Tested on **OrangePi Zero 3**.

---

## 📦 Requirements

* Orange Pi running a Debian/Ubuntu-based OS
* Root or sudo access
* Internet connection
* (Optional) Docker & Docker Compose installed

---

## ✅ Option 1: Install Pi-hole (One-Step Automated Method)

Run the following command to install Pi-hole using the official script:

```bash
curl -sSL https://install.pi-hole.net | bash
```

> This launches the interactive setup to guide you through the Pi-hole configuration.

## 🔧 Step 2: Fix DNS Issues on Reboot (Applies to All Installs)

Download the `restart-dns.sh` script:

```bash
wget https://raw.githubusercontent.com/RGPtv/Orangepi-Pi-hole-Setup/refs/heads/main/restart-dns.sh -O /usr/local/bin/restart-dns.sh
chmod +x /usr/local/bin/restart-dns.sh
```

Schedule it to run at boot:

```bash
crontab -e
```

Add the following line:

```bash
@reboot sleep 5 && /usr/local/bin/restart-dns.sh
```

> ⚠️ Keep the `sleep 5` to allow the system to fully boot before running the script.

## ✅ Optional: Test the DNS Restart Script

To verify the script manually:

```bash
/usr/local/bin/restart-dns.sh
```

Reboot to confirm it works automatically:

```bash
sudo reboot
```
---

## 🐳 Option 2: Install Pi-hole Using Docker

If you prefer a containerized setup:

### Step 1: Create a Project Directory

```bash
mkdir /docker/pihole
cd /docker/pihole
```

### Step 2: Download and Configure `docker-compose.yml`

```bash
wget https://raw.githubusercontent.com/RGPtv/Orangepi-Pi-hole-Setup/refs/heads/main/docker-compose.yml
nano docker-compose.yml
```

> Edit the file to match your network settings and change PiHole password.

### Step 3: Launch Pi-hole

```bash
docker compose up -d
```

> Pi-hole will now run as a background container.

###  Step 4: Create a system service to automatically start your Pi-hole.

```bash
nano /etc/systemd/system/pihole.service
```

and copy this code:

```bash
[Unit]
Description=Pi-hole Service
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

###  Step 5: reload the services and start the service.

```bash
sudo systemctl daemon-reload
sudo systemctl start pihole.service
sudo systemctl status pihole.service
```

---

## 🙌 Credits

Created by [RGPtv](https://github.com/RGPtv)

Pi-hole is a registered trademark of Pi-hole LLC.
