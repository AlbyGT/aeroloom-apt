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
[![POSIX Compliant](https://img.shields.io/badge/Shell-POSIX%20%2F%20dash-brightgreen.svg)](#trustworthiness--production-grade-engineering)
[![Idle Memory](https://img.shields.io/badge/Idle%20RAM-0.00%20MB-success.svg)](#memory--storage-comparison-vs-competitors)
[![Disk Footprint](https://img.shields.io/badge/Disk%20Usage-~8%20MB-success.svg)](#memory--storage-comparison-vs-competitors)
[![Platform](https://img.shields.io/badge/Platform-Debian%20%7C%20Ubuntu%20LTS-orange.svg)](#step-1-installation)

*Deploy hardened, production-ready HTTPS reverse proxies in 30 seconds — without Docker, without bloated runtimes, and with zero resident memory.*

---

</div>

## Table of Contents
- [Why AeroLoom? (Vs The Competition)](#why-aeroloom-vs-the-competition)
- [Feature Comparison Matrix](#feature-comparison-matrix)
- [Memory & Storage Comparison (Vs Competitors)](#memory--storage-comparison-vs-competitors)
- [Trustworthiness & Production-Grade Engineering](#trustworthiness--production-grade-engineering)
- [Architecture Overview](#architecture-overview)
- [Quickstart Guide (Get Live in 60 Seconds)](#quickstart-guide-get-live-in-60-seconds)
  - [Step 1: Installation](#step-1-installation)
  - [Step 2: Interactive Wizard (aeroloom init)](#step-2-interactive-wizard-aeroloom-init)
  - [Alternative: Declarative Table (apps.conf)](#alternative-declarative-table-appsconf)
- [CLI Reference](#cli-reference)
- [Automated Zero-Idle-Memory Renewals](#automated-zero-idle-memory-renewals)
- [Production Hardening & Safeguards](#production-hardening--safeguards)
- [Uninstall & Decommissioning](#uninstall--decommissioning)

---

## Why AeroLoom? (Vs The Competition)

Every reverse proxy solution available for Linux VPS deployments today forces an unnecessary compromise:

- **Nginx Proxy Manager:** Provides a web UI, but requires Docker, Node.js, Python, and a MariaDB/SQLite database. It consumes **150 MB – 250 MB+ of RAM** and **~1.5 GB of disk** just sitting completely idle. On a 512 MB or 1 GB VPS, it burns 25%–50% of your total system memory before your actual application even boots.
- **Traefik:** Built for Kubernetes and container clusters. It requires a heavy Go runtime (**45 MB – 80 MB RAM**, **150 MB disk**), complex dynamic label syntax, and steep learning curves for simple single-server VPS setups.
- **Caddy:** Modern and user-friendly, but introduces non-standard Caddyfile syntax, runs a persistent Go daemon (**35 MB – 50 MB RAM**), and completely replaces battle-tested Nginx.
- **Certbot + Manual Nginx:** Demands sprawling Python dependencies that frequently break during OS upgrades, relies on brittle cron jobs, consumes **~180 MB of disk**, lacks pre-flight DNS guards, and offers no atomic zero-downtime hot-swap validation.

### The AeroLoom Advantage
1. **0.00 MB Resident Memory:** AeroLoom has **no persistent background daemon**. It is written in 100% strict POSIX shell (`/bin/sh`, compliant with `dash`). Only native Nginx itself runs in RAM (~4 MB).
2. **~8 MB Total Disk Footprint:** No Docker images, no Python environments. Uses `uacme` (an 85 KB pure-C ACME client) instead of Certbot's 180 MB Python tree.
3. **Smart Interactive CLI Wizard:** Run `sudo aeroloom init` to get live in seconds with DNS pre-flight checks and listening port auto-discovery.
4. **Zero-Downtime Hot Swaps:** Integrates atomic certificate staging, cryptographic modulus verification, and `nginx -s reload` so certificates swap without dropping connections.
5. **Zero-Idle-Memory Renewals:** Systemd timers replace long-running renewal daemons. Renewals run as transient oneshot processes that audit certificates and release 100% of memory back to the OS upon completion.

---

## Feature Comparison Matrix

| Feature | **AeroLoom** | **Nginx Proxy Manager** | **Traefik** | **Caddy** | **Certbot + Nginx** |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Idle Memory Overhead (RAM)** | **0.00 MB** *(Zero daemon)* | 150 MB – 250 MB | 45 MB – 80 MB | 35 MB – 50 MB | 0 MB (cron) / 25 MB (daemon) |
| **Active Web Server RAM** | **~4 MB** *(Nginx)* | ~4 MB *(Nginx)* | Included above | Included above | ~4 MB *(Nginx)* |
| **Total Disk Footprint** | **~8 MB – 12 MB** | 1,200 MB – 1,800 MB | 120 MB – 250 MB | 80 MB – 150 MB | 150 MB – 220 MB |
| **Docker Required?** | **No** *(Native POSIX)* | Yes | Optional | No | No |
| **Runtime Dependencies** | None (`/bin/sh`, `dash`) | Node.js + Python + DB | Go Runtime | Go Runtime | Python 3 + 30+ pip packages |
| **Interactive Setup Wizard** | **Yes (`aeroloom init`)** | Web Browser UI | No (CLI / Config) | No (Config file) | Interactive CLI |
| **Instant DNS Pre-Flight Check** | **Yes** *(dig vs public IP)* | No | No | No | No |
| **Local Port Auto-Discovery** | **Yes** *(Node, Docker, etc.)* | No | Docker Provider | No | No |
| **Configuration Format** | **1-Line Table (`apps.conf`)** | SQLite / MySQL DB | YAML / TOML | Caddyfile | Complex Nginx Vhost blocks |
| **Automatic TLS Bootstrap** | **Yes** *(Self-Signed $\rightarrow$ ACME)* | No | Yes | Yes | No (Fails if DNS unpointed) |
| **Renewal Mechanism** | **Systemd Timer (Oneshot)** | Resident Node.js cron | Resident Go daemon | Resident Go daemon | Python timer / cron |
| **Zero-Downtime TLS Hot-Swap** | **Yes** *(nginx -s reload)* | Yes | Yes | Yes | Reload command |
| **Ideal VPS Size** | **Any (512 MB – 64 GB+)** | $\ge$ 2 GB RAM | $\ge$ 1 GB RAM | $\ge$ 1 GB RAM | $\ge$ 1 GB RAM |

---

## Memory & Storage Comparison (Vs Competitors)

### 1. Memory Usage Breakdown

AeroLoom introduces **0.00 MB idle memory overhead**. Because it is compiled as an on-demand POSIX utility, it leaves zero processes running in RAM between requests.

```
Resident RAM Consumption at Idle:

AeroLoom:           [0 MB] (Zero Daemon) + Nginx [~4 MB]  = ~4 MB Total
Caddy:              [██████████████████ 40 MB]
Traefik:            [████████████████████████████ 60 MB]
Nginx Proxy Mgr:    [██████████████████████████████████████████████████████████ 200 MB]
```

### 2. Storage Footprint Breakdown

AeroLoom achieves a **>99% disk space reduction** compared to Docker-based solutions and **>90% reduction** compared to Certbot:

| Component | AeroLoom | Certbot + Nginx | Nginx Proxy Manager |
| :--- | :---: | :---: | :---: |
| **CLI / Proxy Manager** | **68 KB** | 150 MB (Python + libs) | 1,500 MB (Docker + NPM images) |
| **ACME Client** | **85 KB** (`uacme` in pure C) | Included above | Included above |
| **Nginx Web Server** | **~4 MB** | ~4 MB | Included above |
| **Database Engine** | **0 KB** (Plain text table) | 0 KB | 50 MB – 150 MB (MariaDB/SQLite) |
| **Per-Domain SSL & Configs** | **~4.5 KB** per domain | ~5 KB per domain | Stored in DB |
| **Total Disk Usage** | **~8 MB – 12 MB** | **~155 MB – 220 MB** | **~1,200 MB – 1,800 MB** |

---

## Trustworthiness & Production-Grade Engineering

AeroLoom is built from the ground up for mission-critical production servers:

1. **GPG-Signed Repository Integrity:**
   - Every package release and metadata index in this repository is cryptographically signed using GPG (`InRelease` / `Release.gpg`).
   - Ubuntu and Debian verify signatures automatically against the armored archive keyring before unpacking.

2. **100% Auditable Pure POSIX Shell:**
   - Zero opaque compiled binary blobs.
   - The entire AeroLoom engine is inspectable in [`bin/aeroloom`](https://github.com/AlbyGT/AeroLoom/blob/main/bin/aeroloom) and verified for strict POSIX shell compliance (`/bin/sh`, 100% compatible with `dash` and `bash`).
   - No telemetry, no external analytic tracking, and no phone-home mechanisms.

3. **Cryptographic Modulus Validation:**
   - Before any certificate is activated, AeroLoom extracts the public key modulus (`openssl x509 -modulus`) and verifies that it mathematically matches the private key's RSA modulus (`openssl rsa -modulus`). A corrupted key or certificate will never be applied to Nginx.

4. **Zero-Downtime Safe Fallbacks:**
   - If ACME validation fails (e.g. DNS propagation delay or Let's Encrypt rate limit), AeroLoom **preserves existing fallback certificates** intact. Nginx never crashes, and incoming visitor traffic is never disrupted.

5. **Sandboxed Systemd Execution:**
   - Background renewal services run under strict Linux sandboxing: `PrivateTmp=true`, `ProtectSystem=full`, `ProtectHome=true`, and restricted `ReadWritePaths` limited only to `/etc/aeroloom` and the ACME webroot.

6. **Continuous Automated Quality Verification:**
   - Every commit and release undergoes automated end-to-end testing across Debian 11/12 and Ubuntu 20.04/22.04/24.04, including race-condition testing, certificate rollover verification, and simulated network outages.

---

## Architecture Overview

```
 [ Daily 03:00 + Jitter ]
 aeroloom-renew.timer
         │
         ▼
 aeroloom-renew.service (Type=oneshot, sandboxed)
         │
         ▼
  aeroloom renew ────────┐
                         ▼
             /etc/aeroloom/apps.conf
                         │
                         ▼
             ┌─────────────────────────┐
             │    needs_issuance()     │
             │  - Issuer == Subject?   │
             │  - Expiring <= 30 days? │
             └───────────┬─────────────┘
                         │
         ┌───────────────┴───────────────┐
         │ [Needs Issuance]              │ [Valid > 30d]
         ▼                               ▼
┌───────────────────────┐   ┌───────────────────────────────┐
│  issue_domain_cert()  │   │  Skip Issuance (Silent Exit)  │
│  uacme against        │   │  (Zero reload, zero memory)   │
│  /var/www/acme-       │   └───────────────────────────────┘
│  challenge webroot    │
└──────────┬────────────┘
           │
    ┌──────┴──────────────────────┐
    │ [Success]                   │ [Failure]
    ▼                             ▼
┌─────────────────────────┐   ┌───────────────────────────────┐
│ Atomic Install          │   │ Log Error to stderr/journald  │
│ -> /etc/aeroloom/ssl/   │   │ (DNS mismatch, reachability,  │
│ nginx -s reload         │   │ rate limit, webroot missing)  │
│ (Zero-Downtime Hot Swap)│   │ Fallback Cert Left Intact     │
└─────────────────────────┘   └───────────────────────────────┘
```

---

## Quickstart Guide (Get Live in 60 Seconds)

### Step 1: Installation

Supported on **Debian 11 (Bullseye), 12 (Bookworm)** and **Ubuntu 20.04 (Focal), 22.04 (Jammy), 24.04 (Noble)**:

```sh
# 1. Setup keyring directory and fetch GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo apt-get update && sudo apt-get install -y --no-install-recommends curl gnupg

curl -fsSL "https://albygt.github.io/aeroloom-apt/keys/aeroloom-archive-keyring.asc" \
  | gpg --dearmor \
  | sudo tee /etc/apt/keyrings/aeroloom-archive-keyring.gpg > /dev/null

sudo chmod 0644 /etc/apt/keyrings/aeroloom-archive-keyring.gpg

# 2. Add AeroLoom APT repository
echo "deb [signed-by=/etc/apt/keyrings/aeroloom-archive-keyring.gpg] https://albygt.github.io/aeroloom-apt/ stable main" \
  | sudo tee /etc/apt/sources.list.d/aeroloom.list

# 3. Synchronize package lists and install AeroLoom
sudo apt-get update
sudo apt-get install -y --no-install-recommends aeroloom
```

---

### Step 2: Interactive Wizard (`aeroloom init`)

Run the onboarding wizard to configure an application in 30 seconds:

```sh
sudo aeroloom init
```

```text
================================================================================
    ___                     __                         
   /   | ___  _________  / /   ____  ____  ____ ___ 
  / /| |/ _ \/ ___/ __ \/ /   / __ \/ __ \/ __ `__ \
 / ___ /  __/ /  / /_/ / /___/ /_/ / /_/ / / / / / /
/_/  |_\___/_/   \____/_____/\____/\____/_/ /_/ /_/ 
      Automated Reverse Proxy & ACME TLS Manager
              Developed by AlbyGT
================================================================================
Welcome to the AeroLoom Interactive Setup Wizard!
This utility provisions an application domain with zero-downtime HTTPS.

1. Enter public domain name (e.g. api.example.com): api.myapp.com

--> Checking DNS A record for api.myapp.com...
    [✓] DNS verified! api.myapp.com resolves to 198.51.100.42

2. Select routing type:
   [1] Reverse Proxy HTTP/WebSocket traffic to a backend service (Default)
   [2] Serve static files from a local directory root
   Choice [1/2, default: 1]: 1

3. Enter upstream backend port or host:port:
   Auto-detected active local services:
     [1] 3000 (node)
     [2] 8080 (docker-proxy)
     [m] Enter custom port or host:port manually

   Select option [1-2] or enter port directly [default: 1]: 1
   [✓] Verified backend reachable on 127.0.0.1:3000

4. Request Let's Encrypt TLS certificate via ACME now? [Y/n]: y
==> Updating /etc/aeroloom/apps.conf...
==> Synchronizing Nginx vhosts and obtaining TLS certificates...
==> Successfully hot-swapped TLS context for 'api.myapp.com' (zero downtime).

================================================================================
  SUCCESS! Your application has been configured with AeroLoom.
================================================================================
  Domain:       https://api.myapp.com
  Routing:      proxy -> 127.0.0.1:3000
  TLS Status:   Let's Encrypt CA-Signed Certificate (Active)
  Nginx Vhost:  /etc/nginx/conf.d/aeroloom.conf
  Auto-Renewal: Active via systemd timer (aeroloom-renew.timer)
================================================================================
```

> [!TIP]
> **DNS Not Pointed Yet?** If DNS hasn't propagated, AeroLoom displays the exact A-record to add, giving you the choice to **[1] Retry**, **[2] Continue with fallback SSL** (Nginx starts proxying immediately, and AeroLoom's timer auto-upgrades to Let's Encrypt once DNS propagates), or **[3] Exit**.

---

### Alternative: Declarative Table (`apps.conf`)

If you prefer configuration-as-code or GitOps, manage all domains in `/etc/aeroloom/apps.conf`:

```conf
# /etc/aeroloom/apps.conf
# <domain>             <action>  <target>
api.example.com        proxy     127.0.0.1:3000
app.example.com        proxy     127.0.0.1:8080
static.example.com     root      /var/www/static
```

Apply and provision certificates with a single command:
```sh
sudo aeroloom sync
```

---

## CLI Reference

| Command | Description |
|---|---|
| `sudo aeroloom init` | Interactive onboarding wizard with DNS pre-check and port auto-discovery |
| `sudo aeroloom sync` | Idempotent sync: compiles vhosts, reloads Nginx, and issues/renews certificates |
| `sudo aeroloom renew` | Silent background renewal audit (used by systemd timer) |
| `sudo aeroloom renew -v` | Verbose renewal audit showing expiration days for each domain |
| `sudo aeroloom compile` | Compiles vhosts and bootstraps self-signed certs (no ACME network calls) |
| `sudo aeroloom issue <domain>` | Manually requests and hot-swaps certificate for a single domain |
| `sudo aeroloom check <domain>` | Checks whether a domain requires issuance or renewal |
| `aeroloom --version` | Displays installed AeroLoom version |
| `aeroloom --help` | Displays usage and configuration path details |

---

## Automated Zero-Idle-Memory Renewals

AeroLoom ships with pre-configured systemd units (`aeroloom-renew.timer` and `aeroloom-renew.service`):

- **No Long-Running Processes:** Runs once per day at 03:00 (with randomized jitter to prevent CA spikes).
- **Silent Auditing:** Certificates valid for $>30$ days are skipped with **zero Nginx reloads** and **zero log clutter**.
- **Security Sandboxed:** Runs in a hardened systemd sandbox with `PrivateTmp=true`, `ProtectSystem=full`, and `ProtectHome=true`.

Check your renewal timer status anytime:
```sh
systemctl status aeroloom-renew.timer
```

---

## Production Hardening & Safeguards

1. **Race-Free Concurrency:** Uses atomic process mutex directory locks (`/var/run/aeroloom.lock`). Concurrent executions or cron overlaps exit cleanly without file corruption.
2. **Atomic Certificate Staging:** Certificates and keys are generated in an isolated `.staging` directory on the same filesystem. They are validated with `openssl` before being atomically moved into place.
3. **Cryptographic Modulus Validation:** Before reloading Nginx, AeroLoom cryptographically verifies that the public certificate's modulus matches the private key's modulus.
4. **Post-Reload Handshake Probing:** Probes the live port 443 TLS context (`openssl s_client`) to ensure Nginx successfully loaded the new certificate into memory before confirming hot-swap success.
5. **Rate-Limit Guard:** Validates DNS records and HTTP port 80 accessibility before querying Let's Encrypt / ZeroSSL, preventing rate-limit bans caused by unresolvable domains.
6. **ACME Staging Flag:** Pass `--staging` to any command to test issuance against Let's Encrypt's staging API with unlimited quotas.

---

## Uninstall & Decommissioning

AeroLoom includes a clean uninstaller:

```sh
# Standard uninstall (removes binary & systemd units, preserves configs & SSL certificates):
sudo ./uninstall.sh

# Complete purge (removes all certificates, ACME data, and configs):
sudo ./uninstall.sh --purge
```

---

## Repository Details

- **Public Repository URL:** `https://albygt.github.io/aeroloom-apt/`
- **Architectures:** `all`, `amd64`, `arm64`
- **Distribution:** `stable`
- **Component:** `main`
- **Main Project & Documentation:** [github.com/AlbyGT/AeroLoom](https://github.com/AlbyGT/AeroLoom)

Maintained by [AlbyGT](https://github.com/AlbyGT). Distributed under the MIT License.
