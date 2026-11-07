# AeroLoom Official APT Repository

Official Debian & Ubuntu package repository for [AeroLoom](https://github.com/AlbyGT/AeroLoom).

Ultra-lightweight reverse proxy and zero-downtime ACME SSL manager.

---

## Installation Instructions (Debian 11/12 & Ubuntu 20.04/22.04/24.04)

```sh
# 1. Install prerequisites and setup keyring directory
sudo install -m 0755 -d /etc/apt/keyrings
sudo apt-get update && sudo apt-get install -y curl gnupg

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
sudo apt-get install -y aeroloom

# 5. Launch the interactive onboarding wizard
sudo aeroloom init
```

---
Maintained by [AlbyGT](https://github.com/AlbyGT).

