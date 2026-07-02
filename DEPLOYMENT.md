# 🛠️ Personal Deployment Guide

>**Credit Notice:** This repository is a public fork of the excellent [expurgate-solo](https://github.com/smck83/expurgate-solo) project developed by **smck83**. All core SPF resolution logic and application architecture belong entirely to the original author.

### 📋 Why this fork exists
The original repository's pre-compiled Docker images do not work smoothly with **ARM64**. To resolve this limitation, this fork runs an automated background pipeline that:
1. Dynamically strips out a deprecated package dependency `ntp` on-the-fly during compilation.
2. Builds a native **Multi-Architecture** image supporting both **ARM64** and **x86-64** seamlessly.

---
&nbsp;
## ⚠️ Pre-Deployment Requirement: Free Up Port 53

Before running the docker-compose file, **ensure port 53 is not being used** by the host system. By default, systems like Ubuntu run a local DNS stub listener (`systemd-resolved`) that hogs port 53.

If you encounter a `failed to bind host port 0.0.0.0:53/udp: address already in use` error, run the following commands on your host to free it up:

```bash
# 1. Disable the systemd DNS stub listener
sudo mkdir -p /etc/systemd/resolved.conf.d/
echo -e "[Resolve]\nDNSStubListener=no" | sudo tee /etc/systemd/resolved.conf.d/no-stub.conf

# 2. Link directly to the upstream resolv.conf
sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf

# 3. Restart the DNS service
sudo systemctl restart systemd-resolved
```
---
&nbsp;
## 🐳 Docker Compose Deployment

You do not need to clone or compile code on your server. Simply create a `docker-compose.yml` file on your host machine and use the configuration below.

```yaml
services:
  spf-solo:
    image: ghcr.io/sujitph/spf-solo:latest
    container_name: spf-solo
    restart: unless-stopped
    ports:
      - "53:53/udp"
      - "9001:9001" # For Supervisord
    environment:
      - ZONE=_spf.yourdomain.com
      - MY_DOMAINS=yourdomain.com yourdomain2.com yourdomain3.com
      - SOURCE_PREFIX=_sd6sdyfn
      - DELAY=300
      - TZ=Australia/Sydney
      - SUPERVISOR_PW=Expurgate
    dns:
      - 1.1.1.1
      - 8.8.8.8

```
---
&nbsp;
## 🚀 Standard Maintenance Commands

### To Start:
```bash
docker compose up -d
```
### To Shutdown:
```bash
docker compose down
```
### To Update:
```bash
docker compose down
docker compose pull
docker compose up -d
```
---
&nbsp;
## 🌐 Supervisord Web Interface

Expurgate Solo uses [Supervisord](http://supervisord.org/) to manage the resolver and rbldnsd processes. A basic web UI is available to monitor the process:
| Setting | Value |
| :--- | :--- |
| **URL** | `http://<your-vm-ip>:9001` |
| **Username** | `admin` |
| **Default Password** | `Expurgate` |

> 🔒 **Security Note:** It is highly recommended not to expose port 9001 to public internet. Use localhost or services like tailscale, netbird, etc to access the web UI. You can change the default password by adding the `SUPERVISOR_PW` variable to the `environment` section of your `docker-compose.yml` file.
---
&nbsp;
## ℹ️ Looking for Application Support?

If you stumbled upon this repository fork and need help understanding the underlying SPF flattening logic, advanced configuration parameters, or core troubleshooting, please visit the official upstream project page:

👉 **[Official smck83/expurgate-solo Repository](https://github.com/smck83/expurgate-solo)**

* Use the link above strictly for assistance with the original application behavior and core configurations.
* Please do not ask the original author for support related to any modifications, multi-architecture compilation or adjustments unique to this fork.
* This repository is provided purely as an automated container build tool for personal use and offers no active support or custom feature maintenance.
