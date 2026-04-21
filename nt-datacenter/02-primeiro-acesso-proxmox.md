---
title: "Primeiro acesso — Painel do cliente → Console → SSH"
description: "Nos templates da NTWeb a senha do root é gerada no primeiro boot e gravada no próprio servidor em:"
slug: "primeiro-acesso-painel-do-cliente-console-ssh"
category: "NT Datacenter"
author:
  name: "Equipe NTWeb"
  role: "Suporte Técnico NTWeb"
  url: "https://ntweb.com.br/sobre"
published: "2026-04-20"
modified: "2026-04-20"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter/primeiro-acesso-painel-do-cliente-console-ssh"
audience: "Misto"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/primeiro-acesso-painel-do-cliente-console-ssh.jpg"
---

# Primeiro acesso — Painel do cliente → Console → SSH

> **Resposta rápida (40–60 palavras):**  
> Depois do primeiro pagamento o sistema provisiona a VM em poucos minutos e envia o IP público por e-mail. Acesse via **SSH na porta 2222 como root**, usando a senha gerada no primeiro boot em `/root/credentials.txt`. Alternativa: abrir o console noVNC direto no Painel do Cliente. Troque a senha imediatamente e suba sua chave SSH.

> **Status:** Draft v0.1  
> **Público:** PJ

## 1. Fluxo completo após o primeiro pagamento

1. **WHMCS provisiona automaticamente** a VM/container no Proxmox (módulo *Proxmox VE VPS & Cloud* da ModulesGarden).
2. Você recebe o e-mail de boas-vindas com:
   - IP público (IPv4 dedicado).
   - Link do Painel do Cliente (`area.ntweb.com.br` TODO).
   - ID da VM / container (VMID).
3. No Painel do Cliente → **Meus Serviços → clique no servidor** para acessar:
   - **Console Web (noVNC / SPICE)**.
   - Botões de **Start / Stop / Reboot**.
   - Estatísticas de CPU / RAM / rede.
   - Link para reinstalar SO.

## 2. Onde pegar a senha inicial

Nos templates da NTWeb a senha do `root` é **gerada no primeiro boot** e gravada no próprio servidor em:

```
/root/credentials.txt
```

Para lê-la na primeira vez, use o console noVNC (não há senha conhecida antes do primeiro boot terminar). Alternativa: alguns templates também entregam a senha via **cloud-init user-data** do módulo WHMCS — nesse caso ela aparece na tela de detalhes do serviço TODO confirmar.

> Existe também o `/root/LEIA-ME.txt`, que contém o manual pós-deploy específico do template (portas, URLs, ações manuais).

## 3. Entrar via console Web (noVNC)

1. Painel do Cliente → serviço → botão **Console**.
2. A janela abre o noVNC já autenticado via proxy `191.7.26.130` TODO DNS.
3. Login: `root` + senha do `credentials.txt`.
4. Se o teclado vier em layout errado, rode `loadkeys br-abnt2` (Linux) antes de digitar a senha.

## 4. Entrar via SSH (recomendado)

### ⚠️ Porta SSH padrão da NTWeb: **2222**

Todos os templates (base e app) já vêm com:
```
Port 2222
PermitRootLogin yes
```

Conexão:

```bash
ssh -p 2222 root@SEU-IP-PUBLICO
```

Na primeira conexão:

1. Aceite o fingerprint.
2. Digite a senha do `/root/credentials.txt`.
3. **Troque a senha** imediatamente com `passwd` e, melhor ainda, desabilite login por senha depois de subir sua chave SSH:
   ```bash
   ssh-copy-id -p 2222 root@SEU-IP-PUBLICO
   # depois, em /etc/ssh/sshd_config.d/90-ntweb.conf:
   # PasswordAuthentication no
   systemctl reload ssh   # Debian/Ubuntu
   # ou: systemctl reload sshd   # AlmaLinux/Rocky
   ```

### RDP (Windows KVM)

- Porta padrão 3389 — abra no firewall do Proxmox antes (ver `06-redes-ip-bridge-vlan.md`).
- Conecte com Remote Desktop / mstsc.

## 5. Primeiros comandos pós-boot (Linux)

```bash
cat /root/LEIA-ME.txt       # manual imutável do template
cat /root/credentials.txt   # runtime (senhas e IP)
hostnamectl                 # confira hostname
timedatectl                 # confira fuso horário (ajuste para America/Sao_Paulo)
apt update && apt upgrade   # Debian/Ubuntu
dnf upgrade                 # AlmaLinux/Rocky
```

## 6. Checklist "saí do zero"

- [ ] Entrei pelo noVNC e vi a tela de login.
- [ ] Li `/root/credentials.txt` e copiei a senha para um gerenciador.
- [ ] Entrei via SSH na porta **2222**.
- [ ] Troquei a senha do root.
- [ ] Subi minha chave SSH.
- [ ] (opcional) Desabilitei `PasswordAuthentication`.
- [ ] Ajustei timezone.

## 7. Quando abrir ticket

- Console noVNC não abre (erro de autenticação do proxy TODO 191.7.26.130 whitelist).
- Não recebi o e-mail de boas-vindas mesmo após o pagamento confirmado.
- `/root/credentials.txt` está vazio (template pode ter falhado o `app-boot.service`).