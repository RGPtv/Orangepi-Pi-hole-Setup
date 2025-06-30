## Step 1: Setup Pi-hole using this command: One-Step Automated Install from https://github.com/pi-hole/pi-hole
```bash
curl -sSL https://install.pi-hole.net | bash
```
## Step 2: Download this script to prevent (DNS Server Error) when it reboot.
```bash
wget 
```
## Step 3: Create a schedule tasks using crontab.
```bash
crontab -e
```
## Last 4: Input this command in crontab and save it (PS: Don't change the sleep time since the system need to boot up first before running the script).
```bash
@reboot sleep 5 && /usr/local/bin/restart-dns.sh
```
