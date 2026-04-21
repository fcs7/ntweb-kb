---
title: "SSH, RDP e noVNC — os 3 jeitos de acessar seu servidor Proxmox"
description: "Use SSH na porta 2222 para Linux, RDP 3389 para Windows KVM e noVNC (console Web) do Painel do Cliente para emergência. Guia prático NTWeb."
slug: "ssh-rdp-novnc"
category: "NT Datacenter"
tags: ["ssh", "rdp", "novnc", "spice", "acesso remoto", "proxmox"]
author:
  name: "Equipe NTWeb"
  role: "Engenharia de Infraestrutura"
  url: "https://ntweb.com.br/sobre"
publisher:
  name: "NTWeb"
  url: "https://ntweb.com.br"
published: "2026-04-20"
modified: "2026-04-21"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter/ssh-rdp-novnc"
audience: "PJ técnico"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/ssh-rdp-novnc.jpg"
references:
  - "https://man.openbsd.org/sshd_config"
  - "https://pve.proxmox.com/wiki/Console"
  - "https://www.spice-space.org/"
---

# SSH, RDP e noVNC — os 3 jeitos de acessar seu servidor

> **Resposta rápida (40–60 palavras):**  
> Acesse seu servidor NTWeb por **SSH na porta 2222** (Linux, dia a dia), **RDP 3389** (Windows KVM) ou **noVNC pelo Painel do Cliente** (emergência, rede caiu, instalação por ISO). A senha inicial do `root` fica em `/root/credentials.txt`. Troque por chave SSH no primeiro login e desligue `PasswordAuthentication` depois que a chave entrar.

> **Público:** PJ (técnico) · **Leitura:** 5 min

## 1. Resumo rápido

| Acesso | Quando usar | Onde |
|---|---|---|
| **SSH (:2222)** | Dia a dia operacional em Linux | Terminal / PuTTY |
| **RDP (:3389)** | Dia a dia em Windows KVM | Remote Desktop / mstsc |
| **noVNC / SPICE** | Emergência, instalação, rede caiu, troubleshoot | Painel do Cliente |

## 2. SSH (porta 2222)

Todos os templates NTWeb sobem com:
```
Port 2222
PermitRootLogin yes
```

### Primeiro login

```bash
ssh -p 2222 root@SEU-IP
```
Senha: a gerada em `/root/credentials.txt` no primeiro boot.

### Hardening imediato (recomendado)

```bash
# 1. Trocar senha do root
passwd

# 2. Subir sua chave pública
ssh-copy-id -p 2222 root@SEU-IP

# 3. Desabilitar senha (depois de confirmar que a chave entra)
cat > /etc/ssh/sshd_config.d/90-ntweb.conf <<EOF
PasswordAuthentication no
PubkeyAuthentication yes
EOF

systemctl reload ssh   # Debian/Ubuntu
# ou:
systemctl reload sshd  # AlmaLinux/Rocky
```

### Criar usuário não-root (boas práticas)

```bash
adduser fulano
usermod -aG sudo fulano     # Debian/Ubuntu
# ou:
usermod -aG wheel fulano    # AlmaLinux/Rocky
```

### Config no seu `~/.ssh/config`

```
Host meuserver
  HostName SEU-IP
  Port 2222
  User root
  IdentityFile ~/.ssh/id_ed25519
```
Depois: `ssh meuserver`.

## 3. RDP em Windows KVM

- Porta padrão: **3389** (TCP).
- Antes de conectar, libere a porta:
  - No **firewall do Proxmox** (painel do cliente → Firewall → adicionar regra).
  - No firewall do **Windows** da VM (geralmente já permitido para a rede "Domínio").
- Cliente:
  - Windows: Remote Desktop (`mstsc`).
  - macOS/Linux: Remmina, Microsoft Remote Desktop.

### Trocar a porta padrão (opcional, recomendado)

Registry do Windows:
```
HKLM\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\PortNumber
```
Reinicie; abra a nova porta no Proxmox Firewall; fecha 3389.

## 4. noVNC (console Web)

Quando usar:
- Rede da VM caiu e SSH não responde.
- Instalando SO manualmente via ISO.
- Quer olhar a tela no momento do boot.

### Como abrir

1. Painel do Cliente → serviço → **Console**.
2. Abre uma janela do navegador com o console em HTML5 — não precisa de VNC Viewer.
3. Login com `root` + senha do `credentials.txt`.

### Dicas
- Layout errado: `loadkeys br-abnt2` (Linux) ou painel do Windows para trocar.
- O noVNC passa por um **proxy** em `191.7.26.130` TODO DNS — se der erro de autenticação, provavelmente o IP do WHMCS não está na whitelist (abra ticket).

### SPICE (alternativa)
- Requer cliente local (virt-viewer).
- Melhor experiência de cursor e áudio do que noVNC.
- Disponível em KVM, não em LXC.

## 5. Qual porta / protocolo em cada template

| Template | Acesso recomendado |
|---|---|
| Base Debian/Ubuntu/AlmaLinux/Rocky | SSH 2222 |
| App n8n (5101) | HTTPS / porta 5678 + SSH 2222 |
| App Ollama/OpenWebUI (5102, 5107) | HTTP :3000 + SSH 2222 |
| App Coolify (5103) | HTTP :8000 + SSH 2222 |
| App LAMP/LEMP (5104, 5105) | HTTP :80 + SSH 2222 |
| App Zabbix (5106) | HTTP :80 (Admin/zabbix) + SSH 2222 |
| App Claude Code / Gemini CLI (5108, 5109) | SSH 2222 apenas |
| App FreePBX (5110) | HTTP :80 `/admin` + SSH 2222 + SIP 5060 |

## 6. Troubleshooting

- **SSH pede senha mesmo após ssh-copy-id:** verifique permissões `chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys`.
- **"Connection refused" na porta 2222:** serviço ssh caiu (`systemctl status ssh`) ou firewall do Proxmox está bloqueando — abra ticket.
- **noVNC abre tela preta:** VM está desligada ou em pausa — botão Start no painel.
- **RDP não conecta:** cheque a regra no Firewall do Proxmox **e** o Windows Firewall da VM.

## 7. Quando abrir ticket

- noVNC retorna "autenticação falhou" repetidamente (whitelist do proxy).
- Perdi acesso SSH e a senha do root não confere com `credentials.txt`.
- Preciso de reset de senha e o console também não aceita.