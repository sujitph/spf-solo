# 🛠️ Personal Deployment Guide

>**Credit Notice:** This repository is a public fork of the excellent [expurgate-solo](https://github.com/smck83/expurgate-solo) project developed by **smck83**. All core SPF resolution logic and application architecture belong entirely to the original author.

### 📋 Why this fork exists
The original repository's pre-compiled Docker images do not work smoothly with **ARM64**. To resolve this limitation, this fork runs an automated background pipeline that:
1. Dynamically strips out a deprecated package dependency `ntp` on-the-fly during compilation.
2. Builds a native **Multi-Architecture** image supporting both **ARM64** and **x86-64** seamlessly.

---
&nbsp;
## 🐳 Docker Compose Deployment

You do not need to clone or compile code on your server. Simply create a `docker-compose.yml` file on your host machine and use the configuration below:

```yaml

services:
  spf-solo:
    image: ghcr.io/sujitph/spf-solo:latest
    container_name: spf-solo
    ports:
      - "53:53/udp"
      - "9001:9001"
    environment:
      - ZONE=_spf.yourdomain.com
      - MY_DOMAINS=yourdomain.com
      - SOURCE_PREFIX=_sd6sdyfn
    restart: unless-stopped
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
docker compose pull
docker compose up -d
```
---
&nbsp;
## ℹ️ Looking for Application Support?

If you stumbled upon this repository fork and need help understanding the underlying SPF flattening logic, advanced configuration parameters, or core troubleshooting, please visit the official upstream project page:

👉 **[Official smck83/expurgate-solo Repository](https://github.com/smck83/expurgate-solo)**

* Use the link above strictly for assistance with the original application behavior and core configurations.
* Please do not ask the original author for support related to any modifications, multi-architecture compilation or adjustments unique to this fork.
* This repository is provided purely as an automated container build tool for personal use and offers no active support or custom feature maintenance.
