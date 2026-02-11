# 🤖 PicoClaw no Proxmox LXC: Um Agente de IA Leve (<15MB RAM)

Este repositório documenta a implementação do [PicoClaw](https://github.com/sipeed/picoclaw) — um agente de IA minimalista escrito em Go — rodando dentro de um container **LXC (Linux Container)** no **Proxmox VE**.

O objetivo deste projeto foi criar um assistente de IA pessoal, acessível via **Telegram**, que rodasse 24/7 com consumo de recursos praticamente irrelevante.

## 🚀 Por que PicoClaw + LXC?

A maioria das soluções de IA locais exige hardware pesado ou VMs dedicadas. O PicoClaw muda esse jogo:
* **Eficiência Extrema:** O binário consome cerca de **10MB de RAM**.
* **Sem Interface Web Pesada:** Funciona como um gateway para Telegram/Discord.
* **LXC vs VM:** Ao usar LXC no Proxmox (em vez de uma VM completa), compartilhamos o kernel do host, reduzindo o overhead a quase zero.

---

## 🛠️ Stack & Requisitos

* **Hypervisor:** Proxmox VE 9.0 (Debian 13/Trixie base)
* **Container:** LXC (Debian 13 Standard)
* **Agente:** PicoClaw (v0.0.1+)
* **LLM Provider:** [OpenRouter](https://openrouter.ai/) (Acesso a Gemini 2.0 Flash, DeepSeek, Claude, etc.)
* **Interface:** Telegram Bot

---

## ⚙️ Guia de Instalação

### 1. Criação do Container LXC
No Proxmox, crie um CT com os seguintes parâmetros "Sweet Spot":
* **Template:** `debian-13-standard` (ou 12)
* **CPU:** 1 Core
* **RAM:** 128 MB (Swap: 128 MB)
* **Disk:** 2 GB a 4 GB
* **Network:** DHCP (IPv4)
* **Unprivileged:** Sim

### 2. Instalação do PicoClaw
Acesse o terminal do container e baixe o binário.
*Nota: Substitua a URL pela versão mais recente encontrada nas [Releases do GitHub](https://github.com/sipeed/picoclaw/releases).*

```bash
apt update && apt install -y wget nano ca-certificates

# Criar diretório
mkdir -p /opt/picoclaw
cd /opt/picoclaw

# Baixar o binário (exemplo para Linux AMD64)
wget -O picoclaw [https://github.com/sipeed/picoclaw/releases/download/v0.0.1/picoclaw-linux-amd64](https://github.com/sipeed/picoclaw/releases/download/v0.0.1/picoclaw-linux-amd64)

# Tornar executável
chmod +x picoclaw
