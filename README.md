<div align="center">
  <a href="#-picoclaw-in-proxmox-lxc-lightweight-ai-agent">🇺🇸 English</a> |
  <a href="#-picoclaw-no-proxmox-lxc-agente-de-ia-leve">🇧🇷 Português</a>
</div>

---

<a name="-picoclaw-in-proxmox-lxc-lightweight-ai-agent"></a>

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
# Always check for the latest version URL
wget -O picoclaw [https://github.com/sipeed/picoclaw/releases/download/v0.0.1/picoclaw-linux-amd64](https://github.com/sipeed/picoclaw/releases/download/v0.0.1/picoclaw-linux-amd64)

# Make executable
chmod +x picoclaw
```

### 3. Configuration (OpenRouter + Telegram)
Create the config file:

```bash
nano config.json
```

Paste the content below. **Security Warning:** Configure `allowFrom` with your Telegram ID to prevent unauthorized use.

```json
{
  "agents": {
    "defaults": {
      "workspace": "./workspace",
      "model": "google/gemini-2.0-flash-001",
      "max_tokens": 8192,
      "temperature": 0.7
    }
  },
  "providers": {
    "openrouter": {
      "api_key": "sk-or-v1-YOUR-KEY-HERE...",
      "api_base": "[https://openrouter.ai/api/v1](https://openrouter.ai/api/v1)"
    }
  },
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "YOUR-TELEGRAM-BOT-TOKEN",
      "allowFrom": ["YOUR-TELEGRAM-NUMERIC-ID"]
    }
  }
}
```

### 4. Create Systemd Service
To ensure the agent starts on boot:

```bash
nano /etc/systemd/system/picoclaw.service
```

Service content:

```ini
[Unit]
Description=PicoClaw AI Gateway
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/opt/picoclaw
ExecStart=/opt/picoclaw/picoclaw gateway
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
systemctl daemon-reload
systemctl enable picoclaw
systemctl start picoclaw
```

## 🔒 Security

1.  **Whitelist:** The `allowFrom` parameter in `config.json` is mandatory.
2.  **Firewall:** Enable Proxmox Firewall on the LXC with **INPUT DROP** policy (since the container only makes outbound connections).
3.  **Logs:** Disable log retention in your OpenRouter dashboard for privacy.

---

<a name="-picoclaw-no-proxmox-lxc-agente-de-ia-leve"></a>

# 🤖 PicoClaw no Proxmox LXC: Agente de IA Leve

Este repositório documenta a implementação do [PicoClaw](https://github.com/sipeed/picoclaw) — um agente de IA minimalista escrito em Go — rodando dentro de um **Proxmox VE LXC (Linux Container)**.

O objetivo deste projeto foi criar um assistente de IA pessoal, acessível via **Telegram**, rodando 24/7 com consumo de recursos irrelevante (**<15MB RAM**).

## 🚀 Por que PicoClaw + LXC?

A maioria das soluções de IA locais exige hardware pesado ou VMs dedicadas. O PicoClaw muda esse jogo:
* **Eficiência Extrema:** O binário consome cerca de 10MB de RAM.
* **Headless:** Funciona como um gateway para Telegram/Discord (sem interface Web pesada).
* **LXC vs VM:** Ao usar LXC no Proxmox (em vez de uma VM completa), compartilhamos o kernel do host, reduzindo o overhead a quase zero.

## 🛠️ Stack e Requisitos

* **Hypervisor:** Proxmox VE 9.x (Baseado no Debian 13/Trixie)
* **Container:** LXC (Template Debian 13 Standard)
* **Agente:** PicoClaw (v0.0.1+)
* **Provedor LLM:** [OpenRouter](https://openrouter.ai/) (Acesso a Gemini 2.0 Flash, DeepSeek, Claude, etc.)
* **Interface:** Bot do Telegram

## ⚙️ Guia de Instalação

### 1. Criação do Container LXC
No Proxmox, crie um CT com os seguintes parâmetros recomendados:
* **Template:** `debian-13-standard` (ou 12)
* **CPU:** 1 Core
* **RAM:** 128 MB (Swap: 128 MB)
* **Disco:** 2 GB - 4 GB
* **Rede:** DHCP (IPv4)
* **Unprivileged:** Sim

### 2. Instalação do PicoClaw
Acesse o terminal do container e baixe o binário.
*Nota: Verifique a versão mais recente nas [Releases do GitHub](https://github.com/sipeed/picoclaw/releases).*

```bash
apt update && apt install -y wget nano ca-certificates

# Criar diretório
mkdir -p /opt/picoclaw
cd /opt/picoclaw

# Baixar o binário (Exemplo para Linux AMD64)
# Sempre verifique a URL da versão mais recente
wget -O picoclaw [https://github.com/sipeed/picoclaw/releases/download/v0.0.1/picoclaw-linux-amd64](https://github.com/sipeed/picoclaw/releases/download/v0.0.1/picoclaw-linux-amd64)

# Tornar executável
chmod +x picoclaw
```

### 3. Configuração (OpenRouter + Telegram)
Crie o arquivo de configuração:

```bash
nano config.json
```

Cole o conteúdo abaixo, ajustando suas chaves. **Aviso de Segurança:** Configure o `allowFrom` com seu ID do Telegram para evitar uso não autorizado.

```json
{
  "agents": {
    "defaults": {
      "workspace": "./workspace",
      "model": "google/gemini-2.0-flash-001",
      "max_tokens": 8192,
      "temperature": 0.7
    }
  },
  "providers": {
    "openrouter": {
      "api_key": "sk-or-v1-SUA-CHAVE-AQUI...",
      "api_base": "[https://openrouter.ai/api/v1](https://openrouter.ai/api/v1)"
    }
  },
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "SEU-TOKEN-DO-BOTFATHER",
      "allowFrom": ["SEU-ID-NUMERICO-TELEGRAM"]
    }
  }
}
```

### 4. Criar Serviço Systemd
Para garantir que o agente inicie automaticamente com o boot:

```bash
nano /etc/systemd/system/picoclaw.service
```

Conteúdo do serviço:

```ini
[Unit]
Description=PicoClaw AI Gateway
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/opt/picoclaw
ExecStart=/opt/picoclaw/picoclaw gateway
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

Ative e inicie:

```bash
systemctl daemon-reload
systemctl enable picoclaw
systemctl start picoclaw
```

## 🔒 Segurança

1.  **Whitelist:** O parâmetro `allowFrom` no `config.json` é obrigatório.
2.  **Firewall:** Habilite o Firewall no LXC do Proxmox com política de **INPUT DROP** (já que o container só faz conexões de saída).
3.  **Logs:** Desative a retenção de logs no painel do OpenRouter para maior privacidade.

---

## 📄 Credits / Créditos

* **Original Project:** [sipeed/picoclaw](https://github.com/sipeed/picoclaw)
