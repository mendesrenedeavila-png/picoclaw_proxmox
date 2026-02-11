<div align="center">
  <a href="#-picoclaw-in-proxmox-lxc-lightweight-ai-agent">🇺🇸 English</a> |
  <a href="#-picoclaw-no-proxmox-lxc-agente-de-ia-leve">🇧🇷 Português</a>
</div>

---

# 🤖 PicoClaw in Proxmox LXC: Lightweight AI Agent

This repository documents the implementation of [PicoClaw](https://github.com/sipeed/picoclaw) — a minimalist AI agent written in Go — running inside a **Proxmox VE LXC (Linux Container)**.

The goal is to have a personal AI assistant accessible via **Telegram** running 24/7 with negligible resource consumption (**<15MB RAM**).

## 🚀 Why PicoClaw + LXC?

Most local AI solutions require heavy hardware or dedicated VMs. PicoClaw changes the game:
* **Extreme Efficiency:** The binary consumes ~10MB of RAM.
* **Headless:** Works as a gateway for Telegram/Discord (no heavy Web UI).
* **LXC vs VM:** By using LXC on Proxmox instead of a full VM, we share the host kernel, reducing overhead to near zero.

## 🛠️ Stack & Requirements

* **Hypervisor:** Proxmox VE 9.x (Debian 13/Trixie base)
* **Container:** LXC (Debian 13 Standard Template)
* **Agent:** PicoClaw (v0.0.1+)
* **LLM Provider:** [OpenRouter](https://openrouter.ai/) (Access to Gemini 2.0 Flash, DeepSeek, Claude, etc.)
* **Interface:** Telegram Bot

## ⚙️ Installation Guide

### 1. LXC Creation
Create a CT in Proxmox with these "Sweet Spot" parameters:
* **Template:** `debian-13-standard` (or 12)
* **CPU:** 1 Core
* **RAM:** 128 MB (Swap: 128 MB)
* **Disk:** 2 GB - 4 GB
* **Network:** DHCP (IPv4)
* **Unprivileged:** Yes

### 2. Installing PicoClaw
Access the container console and download the binary.
*Note: Check [GitHub Releases](https://github.com/sipeed/picoclaw/releases) for the latest version.*

```bash
apt update && apt install -y wget nano ca-certificates

# Create directory
mkdir -p /opt/picoclaw
cd /opt/picoclaw

# Download binary (Example for Linux AMD64)
wget -O picoclaw [https://github.com/sipeed/picoclaw/releases/download/v0.0.1/picoclaw-linux-amd64](https://github.com/sipeed/picoclaw/releases/download/v0.0.1/picoclaw-linux-amd64)

# Make executable
chmod +x picoclaw
