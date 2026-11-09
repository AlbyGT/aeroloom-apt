# AeroLoom Official APT Repository

<div align="center">

```
    ___                     __                         
   /   | ___  _________  / /   ____  ____  ____ ___ 
  / /| |/ _ \/ ___/ __ \/ /   / __ \/ __ \/ __ `__ \
 / ___ /  __/ /  / /_/ / /___/ /_/ / /_/ / / / / / /
/_/  |_\___/_/   \____/_____/\____/\____/_/ /_/ /_/ 
```

**Official Debian & Ubuntu package repository for [AeroLoom](https://github.com/AlbyGT/AeroLoom).**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/Platform-Debian%20%7C%20Ubuntu%20LTS-orange.svg)](#installation-instructions-debian--ubuntu)
[![Main Repo](https://img.shields.io/badge/Source-AlbyGT%2FAeroLoom-blue.svg)](https://github.com/AlbyGT/AeroLoom)

*Ultra-lightweight, zero-daemon Nginx reverse proxy manager with automated TLS and 0.00 MB resident memory.*

---

</div>

## Installation Instructions (Debian & Ubuntu)

Supported distributions:
- **Debian:** 11 (Bullseye), 12 (Bookworm)
- **Ubuntu:** 20.04 (Focal), 22.04 (Jammy), 24.04 (Noble)

```sh
# 1. Install prerequisites and setup keyring directory
sudo install -m 0755 -d /etc/apt/keyrings
sudo apt-get update && sudo apt-get install -y --no-install-recommends curl gnupg

# 2. Fetch and import the AeroLoom GPG public key
curl -fsSL "https://albygt.github.io/aeroloom-apt/keys/aeroloom-archive-keyring.asc" \
  | gpg --dearmor \
  | sudo tee /etc/apt/keyrings/aeroloom-archive-keyring.gpg > /dev/null

sudo chmod 0644 /etc/apt/keyrings/aeroloom-archive-keyring.gpg

# 3. Add the AeroLoom signed repository entry
echo "deb [signed-by=/etc/apt/keyrings/aeroloom-archive-keyring.gpg] https://albygt.github.io/aeroloom-apt/ stable main" \
  | sudo tee /etc/apt/sources.list.d/aeroloom.list

# 4. Synchronize package lists and install AeroLoom
sudo apt-get update
sudo apt-get install -y --no-install-recommends aeroloom

# 5. Launch the interactive onboarding wizard
sudo aeroloom init
```

---

## Why AeroLoom?

| Feature | **AeroLoom** | **Nginx Proxy Manager** | **Traefik** | **Caddy** | **Certbot + Nginx** |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Idle Memory (RAM)** | **0.00 MB** *(No daemon)* | 150 MB – 250 MB | 45 MB – 80 MB | 35 MB – 50 MB | 0 MB (cron) / 25 MB (daemon) |
| **Active Web Server RAM** | **~4 MB** *(Nginx)* | ~4 MB *(Nginx)* | Included above | Included above | ~4 MB *(Nginx)* |
| **Docker Required?** | **No** *(Native POSIX)* | Yes | Optional | No | No |
| **Runtime Stack** | **POSIX /bin/sh (dash)** | Node.js + Python + DB | Go Runtime | Go Runtime | Python + 30+ pip packages |
| **Setup Wizard** | **Yes (`aeroloom init`)** | Web Browser UI | No (CLI / Config) | No (Config file) | Interactive CLI |
| **Instant DNS Pre-Check** | **Yes** *(dig vs public IP)* | No | No | No | No |
| **Port Auto-Discovery** | **Yes** *(Node, Docker, etc.)* | No | Docker Provider | No | No |
| **Config Format** | **1-Line Table (`apps.conf`)** | SQLite / MySQL DB | YAML / TOML | Caddyfile | Complex Vhost blocks |
| **Zero-Downtime Hot-Swap** | **Yes** *(nginx -s reload)* | Yes | Yes | Yes | Reload command |

---

## Repository Details

- **Public Repository URL:** `https://albygt.github.io/aeroloom-apt/`
- **Architectures:** `all`, `amd64`, `arm64`
- **Distribution:** `stable`
- **Component:** `main`
- **Main Project & Documentation:** [github.com/AlbyGT/AeroLoom](https://github.com/AlbyGT/AeroLoom)

Maintained by [AlbyGT](https://github.com/AlbyGT). Distributed under the MIT License.
