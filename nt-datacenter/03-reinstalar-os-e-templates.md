---
title: "Reinstalar SO e escolher template"
description: "Use quando você quer controle total e vai instalar seus próprios apps."
slug: "reinstalar-so-e-escolher-template"
category: "NT Datacenter"
author:
  name: "Equipe NTWeb"
  role: "Suporte Técnico NTWeb"
  url: "https://ntweb.com.br/sobre"
published: "2026-04-20"
modified: "2026-04-20"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter/reinstalar-so-e-escolher-template"
audience: "Misto"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/reinstalar-so-e-escolher-template.jpg"
---

# Reinstalar SO e escolher template

> **Resposta rápida (40–60 palavras):**  
> Reinstale sua VM no **Painel do Cliente → Reinstalar OS**. Escolha entre **10 templates base** (Debian 11/12/13, Ubuntu 20/22/24/25, AlmaLinux 8, Rocky 8) ou **10 app templates prontos** (n8n, Zabbix, Coolify, LAMP, LEMP, FreePBX, entre outros). Reinstalação apaga tudo — faça snapshot antes. Nova senha root em `/root/credentials.txt` em 2 a 5 minutos.

> **Status:** Draft v0.1  
> **Público:** PJ

## 1. Dois tipos de template disponíveis

### Base Templates — sistemas limpos (VMIDs 5001–5010)

Use quando você quer controle total e vai instalar seus próprios apps.

| Template | Distro | Quando usar |
|---|---|---|
| `tmpl-debian-12` | Debian 12 (bookworm) | Padrão — serve pra quase tudo |
| `tmpl-debian-13` | Debian 13 (trixie) | Kernel mais novo, bleeding edge |
| `tmpl-debian-11` | Debian 11 (bullseye) | Legado, stack antiga |
| `tmpl-ubuntu-2404` | Ubuntu 24.04 LTS | LTS mais novo, ecossistema Ubuntu |
| `tmpl-ubuntu-2204` | Ubuntu 22.04 LTS | LTS estável e muito documentado |
| `tmpl-ubuntu-2004` | Ubuntu 20.04 LTS | Legado Ubuntu |
| `tmpl-ubuntu-2410` | Ubuntu 24.10 | Release interim |
| `tmpl-ubuntu-2504` | Ubuntu 25.04 | Release interim mais recente |
| `tmpl-alma-8` | AlmaLinux 8 | Substituto CentOS 8 RHEL-family |
| `tmpl-rocky-8` | Rocky Linux 8 | Substituto CentOS 8 RHEL-family |

### App Templates — Debian 12 com app pré-instalado (VMIDs 5101–5110)

Use quando quer subir um app em segundos sem se preocupar com setup.

| Template | App | Porta/URL | Notas |
|---|---|---|---|
| `tmpl-app-n8n` (5101) | n8n (automação low-code) | `https://DOMAIN` ou `http://IP:5678` | Senha admin em `/root/credentials.txt`. DOMAIN do cloud-init ativa HTTPS |
| `tmpl-app-ollama-webui` (5102) | Ollama + OpenWebUI | `http://IP:3000` | 1º signup vira admin. Modelos baixam sob demanda |
| `tmpl-app-coolify` (5103) | Coolify | `http://IP:8000` | Setup wizard no 1º acesso |
| `tmpl-app-lamp` (5104) | LAMP | Apache + MariaDB + PHP | Senha MariaDB em `/root/credentials.txt` |
| `tmpl-app-lemp` (5105) | LEMP | Nginx + MariaDB + PHP-FPM | vhost PHP já configurado |
| `tmpl-app-zabbix` (5106) | Zabbix | `http://IP` (Admin/zabbix) | DOMAIN-aware |
| `tmpl-app-openclaw` (5107) | OpenClaw + OpenWebUI | `http://IP:3000` | Rodar `openclaw onboard` via SSH |
| `tmpl-app-claude-code` (5108) | Claude Code CLI | CLI | Precisa `ANTHROPIC_API_KEY` |
| `tmpl-app-gemini-cli` (5109) | Gemini CLI | CLI | Login Google no 1º uso |
| `tmpl-app-freepbx` (5110) | FreePBX | `http://IP/admin` | DOMAIN-aware, installer oficial |

> Todos os app templates trazem `/root/LEIA-ME.txt` (manual imutável: portas, URLs, ações manuais) e `/root/credentials.txt` (senhas geradas no primeiro boot).

## 2. Como reinstalar pelo Painel do Cliente

1. **Painel do Cliente → Meus Serviços → seu servidor.**
2. Clique em **Reinstalar / Reinstall OS**.
3. Escolha o template na lista (o nome exibido é o VMID + descrição, ex.: `5104 — LAMP Stack Debian 12`).
4. Confirme — toda a VM/container vai ser destruída e recriada.
5. Aguarde 2–5 minutos. A senha do `root` é **regerada** no novo `/root/credentials.txt`.

> ⚠️ Reinstalação **apaga tudo**. Se quiser preservar dados, faça snapshot/backup antes (ver `05-snapshots-clones-backup.md`).

## 3. Qual template escolher?

### Roteiro rápido
- **Quer algo específico sem configurar?** Pegue um **App Template** (5101–5110).
- **Quer montar seu próprio stack?** Pegue um **Base Template** Debian 12 (5001) — cobre 90% dos casos.
- **Precisa compatibilidade com script RPM legado?** AlmaLinux 8 (5005) ou Rocky 8 (5006).
- **Precisa última LTS Ubuntu?** Ubuntu 24.04 (5002).

### O que muda entre base e app
- Base vem **limpo**, só com SSH 2222 + `PermitRootLogin yes`.
- App já vem com Docker (nas stacks que usam) + serviço iniciando via `app-boot.service` no primeiro boot.
- **Em ambos** a porta SSH é **2222**.

## 4. Posso usar meu próprio ISO / imagem?

- **KVM:** sim, mas só via ticket — o time carrega o ISO no storage `local:iso` do nó.
- **LXC:** não. LXC só aceita templates criados no formato `pveam` (não ISO).

## 5. Templates DOMAIN-aware (importante)

Os app templates **5101 n8n / 5106 Zabbix / 5110 FreePBX** leem o `Search Domain` entregue pelo cloud-init. Se o módulo WHMCS injeta `DOMAIN`:

- n8n ativa HTTPS automaticamente + `WEBHOOK_URL` do domínio.
- Zabbix / FreePBX usam o domínio na URL e nas credenciais.

Se sem domínio, caem para HTTP no IP público.

## 6. FAQ

- **P:** Depois de reinstalar, perdi o LEIA-ME.txt. Onde pego de novo?  
  **R:** Ele é regravado em toda reinstalação. Se sumiu, abra ticket informando o VMID.
- **P:** Posso trocar de App Template sem reinstalar (ex.: de LAMP para LEMP)?  
  **R:** Não. Reinstalação é sempre do zero.
- **P:** Posso mudar de LXC para KVM só trocando template?  
  **R:** Não — é um produto diferente. Contrate o produto novo e migre dados.

## 7. Quando abrir ticket

- Template desejado não aparece na lista do painel (cron `app-templates:refresh` pode estar atrasado).
- Reinstalação ficou "em andamento" por mais de 15 min.
- `/root/LEIA-ME.txt` ou `/root/credentials.txt` não foram gerados.