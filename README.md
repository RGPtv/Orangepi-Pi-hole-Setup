# Pi-hole Setup Guide for 🍊 OrangePi

A simple setup to install [Pi-hole](https://pi-hole.net/) on an Orange Pi and automatically fix DNS resolution issues after reboot.

It was tested on OrangePi Zero 3.

---

## 📦 Requirements

* Orange Pi running a Debian/Ubuntu-based OS
* Root or sudo access
* Internet connection

---

## ✅ Step 1: Install Pi-hole (One-Step Automated Method)

Run the following command to install Pi-hole using the official automated script:

```bash
curl -sSL https://install.pi-hole.net | bash
```

> This will launch the interactive setup and guide you through Pi-hole configuration.

---

## 🔧 Step 2: Download the DNS Restart Script

Download the `restart-dns.sh` script, which ensures the DNS server starts properly after a reboot:

```bash
wget https://raw.githubusercontent.com/RGPtv/Orangepi-Pi-hole-Setup/refs/heads/main/restart-dns.sh -O /usr/local/bin/restart-dns.sh
chmod +x /usr/local/bin/restart-dns.sh
```

> This script restarts the Pi-hole DNS service after the system boots, preventing DNS errors.

---

## ⏰ Step 3: Create a Schedule the Script to Run at Boot

Open the crontab editor:

```bash
crontab -e
```

Add the following line at the bottom:

```bash
@reboot sleep 5 && /usr/local/bin/restart-dns.sh
```

> ⚠️ **Do not change the `sleep 5` delay.** This allows the system to fully boot before executing the script.

---

## 💪 Optional: Test It Works

You can test the script manually before rebooting:

```bash
/usr/local/bin/restart-dns.sh
```

Then reboot to verify:

```bash
sudo reboot
```

---
## 🙌 Credits

Created by [RGPtv](https://github.com/RGPtv).

Pi-hole is a registered trademark of Pi-hole LLC.
