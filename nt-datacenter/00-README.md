---
title: "NT Datacenter Virtual — índice de artigos (Proxmox VE, KVM e LXC)"
description: "NT Datacenter Virtual é a linha cloud premium da NTWeb sobre Proxmox VE 8.x. Cobre NT Cloud KVM (qualquer SO) e NT Cloud LXC (container Linux)."
slug: "nt-datacenter-virtual"
category: "NT Datacenter"
tags: ["proxmox", "cloud", "kvm", "lxc", "datacenter", "índice"]
author:
  name: "Equipe NTWeb"
  role: "Engenharia de Infraestrutura"
  url: "https://ntweb.com.br/sobre"
publisher:
  name: "NTWeb"
  url: "https://ntweb.com.br"
published: "2026-04-20"
modified: "2026-04-21"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter"
audience: "PJ técnico"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/nt-datacenter-capa.jpg"
references:
  - "https://pve.proxmox.com/wiki/Main_Page"
  - "https://www.proxmox.com/en/proxmox-virtual-environment"
---

# NT Datacenter Virtual — Proxmox VE (KVM e LXC)

> **Resposta rápida (40–60 palavras):**  
> **NT Datacenter Virtual é a linha cloud premium da NTWeb** sobre Proxmox VE 8.x. Cobre dois produtos: **NT Cloud KVM** (virtualização completa, qualquer SO, recursos dedicados) e **NT Cloud LXC** (container Linux, menor overhead). Ambos com teto de 64 GB de RAM, IPv4 dedicado e console noVNC no Painel do Cliente. Comece por [`_intro-virtualizacao-e-containers`](./_intro-virtualizacao-e-containers.md) se o tema é novo.

Produtos WHMCS cobertos por esta pasta:
- **NT Cloud KVM** — virtualização completa (qualquer SO), posicionamento **cloud premium** (alta disponibilidade, recursos dedicados).
- **NT Cloud LXC** — container Linux leve, posicionamento **cloud premium em container** (menor overhead).

> A pasta irmã `nt-vps/` cobre **NT KVM** e **NT LXC** (linha VPS padrão / entrada). O fluxo técnico é o mesmo — artigos aqui servem de referência para ambas as linhas, as diferenças aparecem no artigo 09.

## Dados técnicos da plataforma

| Item | Valor |
|---|---|
| Hipervisor | Proxmox VE 8.x |
| Módulo WHMCS | *Proxmox VE VPS & Cloud* (ModulesGarden) |
| Console Web | noVNC / SPICE via proxy (`191.7.26.130` TODO DNS) |
| SSH default nos templates | porta **2222** com `PermitRootLogin yes` |
| Senha inicial do root | gerada no primeiro boot em `/root/credentials.txt` |
| Manual pós-deploy | `/root/LEIA-ME.txt` (injeção por template) |
| Teto de RAM | **64 GB** (regra do catálogo NT Hosting) |

## Templates disponíveis no painel (visíveis no provisionamento)

### Base (sistemas operacionais limpos, VMIDs 5001–5010)

| VMID | Template | Distro |
|---|---|---|
| 5001 | `tmpl-debian-12` | Debian 12 |
| 5002 | `tmpl-ubuntu-2404` | Ubuntu 24.04 |
| 5003 | `tmpl-debian-13` | Debian 13 (trixie) |
| 5004 | `tmpl-ubuntu-2204` | Ubuntu 22.04 |
| 5005 | `tmpl-alma-8` | AlmaLinux 8 |
| 5006 | `tmpl-rocky-8` | Rocky Linux 8 |
| 5007 | `tmpl-debian-11` | Debian 11 |
| 5008 | `tmpl-ubuntu-2004` | Ubuntu 20.04 |
| 5009 | `tmpl-ubuntu-2410` | Ubuntu 24.10 |
| 5010 | `tmpl-ubuntu-2504` | Ubuntu 25.04 |

### App Templates (Debian 12 + app pré-instalado, VMIDs 5101–5110)

| VMID | App | Porta/UI |
|---|---|---|
| 5101 | n8n | `https://DOMAIN` ou `http://IP:5678` (admin via `/root/credentials.txt`) |
| 5102 | Ollama + OpenWebUI | `http://IP:3000` (1º signup vira admin) |
| 5103 | Coolify | `http://IP:8000` (setup wizard) |
| 5104 | LAMP Stack | Apache/MariaDB/PHP (senha em `/root/credentials.txt`) |
| 5105 | LEMP Stack | Nginx/MariaDB/PHP-FPM |
| 5106 | Zabbix | `http://IP` (Admin/zabbix, banco com senha gerada) |
| 5107 | OpenClaw + OpenWebUI | `http://IP:3000`, rodar `openclaw onboard` via SSH |
| 5108 | Claude Code | CLI — exige `ANTHROPIC_API_KEY` |
| 5109 | Gemini CLI | CLI — login Google no primeiro uso |
| 5110 | FreePBX | `http://IP/admin` |

## Artigo introdutório (lúdico) — leia primeiro

**`_intro-virtualizacao-e-containers.md`** — o que é virtualização, o que é container, explicado com analogia de **apartamento vs. coworking**, e um **quiz "Qual é minha necessidade?"** que leva o cliente até a combinação correta (KVM ou LXC × NT Cloud ou NT VPS).

## Roteiro de artigos técnicos

| Nº | Arquivo | Foco | Público |
|---|---|---|---|
| 01 | `01-kvm-vs-lxc.md` | Quando escolher KVM ou LXC (versão técnica) | PJ |
| 02 | `02-primeiro-acesso-proxmox.md` | Painel do cliente → console → SSH | PJ |
| 03 | `03-reinstalar-os-e-templates.md` | Escolher template base ou app template | PJ |
| 04 | `04-ssh-rdp-novnc.md` | Acesso porta 2222, SSH root, noVNC | PJ |
| 05 | `05-snapshots-clones-backup.md` | Snapshots, clones, backup agendado | PJ |
| 06 | `06-redes-ip-bridge-vlan.md` | IP público, privado, bridge, VLAN | PJ |
| 07 | `07-upgrade-recursos.md` | CPU/RAM/disco sem reinstalar | PJ |
| 08 | `08-metricas-logs-alertas.md` | Monitoramento via Proxmox + WHMCS | PJ |
| 09 | `09-nt-datacenter-vs-nt-vps.md` | Comparativo Cloud vs VPS padrão | PJ |

**Público-alvo geral desta linha:** PJ técnico (equipe de TI, desenvolvedor, administrador de sistemas). Não há versão "leiga" forte nos artigos — quem contrata Proxmox sabe o que é um servidor.