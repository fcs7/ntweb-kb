---
title: "Redes no NT Datacenter — IP público, IP privado, bridge e VLAN"
description: "Cada servidor NTWeb vem com 1 IPv4 público dedicado na bridge vmbr0. IPs extras, rede privada entre seus servidores e VLAN dedicada saem por ticket."
slug: "redes-ip-bridge-vlan"
category: "NT Datacenter"
tags: ["rede", "ipv4", "ipv6", "bridge", "vlan", "firewall", "rdns", "proxmox"]
author:
  name: "Equipe NTWeb"
  role: "Engenharia de Infraestrutura"
  url: "https://ntweb.com.br/sobre"
publisher:
  name: "NTWeb"
  url: "https://ntweb.com.br"
published: "2026-04-20"
modified: "2026-04-21"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter/redes-ip-bridge-vlan"
audience: "PJ técnico"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/redes-ip-bridge-vlan.jpg"
references:
  - "https://pve.proxmox.com/wiki/Network_Configuration"
  - "https://pve.proxmox.com/wiki/Firewall"
---

# Redes — IP público, IP privado, bridge e VLAN

> **Resposta rápida (40–60 palavras):**  
> Cada servidor NT Datacenter já vem com **1 IPv4 público dedicado** ligado à bridge `vmbr0`. IPs extras, rede privada entre seus próprios servidores (VLAN dedicada) e rDNS saem por ticket. Firewall do Proxmox atua antes do SO — configure pelo Painel do Cliente e rode também UFW ou firewalld dentro da VM (defesa em camadas).

> **Público:** PJ (técnico) · **Leitura:** 6 min

## 1. O que você recebe por padrão

| Recurso | Inclui |
|---|---|
| IPv4 público dedicado | 1 (um) |
| IPv6 | TODO (confirmar se entregamos bloco /64 por padrão) |
| Bridge primária | `vmbr0` (internet) |
| Firewall do nó | Regras aplicadas via Painel do Cliente |
| Taxa de transferência (traffic) | Ilimitado (fair use) TODO |

## 2. Bridge, interface e IP

No Proxmox, toda VM/container tem uma ou mais **interfaces de rede** ligadas a uma **bridge** no host. A bridge é como um "switch virtual".

- `vmbr0` — acesso à internet pública (IP público entra aqui).
- `vmbr1` (quando existe) — rede privada entre seus próprios servidores (LAN virtual, sem sair do datacenter).

### Ver as interfaces
```bash
ip -br a
```
Exemplo saída:
```
eth0  UP  177.70.4.15/29  ...
```

## 3. Adicionar IP extra (IP adicional)

Quando faz sentido:
- Você precisa **separar serviços** (ex.: site em 1 IP, SMTP em outro para reputação).
- Tem **CFTV / DVR** publicando em IP dedicado.
- Vai revender hospedagem e quer isolar clientes por IP.

Como pedir:
- Abra ticket no departamento comercial informando VMID + motivo.
- Taxa mensal: TODO.
- Prazo: mesmo dia útil, normalmente.

Configuração dentro da VM (exemplo Debian/Ubuntu):
```bash
# /etc/network/interfaces.d/eth0.cfg
auto eth0:1
iface eth0:1 inet static
  address 177.70.4.20
  netmask 255.255.255.248
```

## 4. Rede privada entre seus servidores

Se você contratou dois ou mais servidores na NTWeb e quer **tráfego interno sem passar pela internet pública**, peça uma **bridge privada** (ex.: `vmbr1` ou VLAN dedicada).

Benefícios:
- Backup entre servidores sem consumir banda pública.
- Banco de dados em servidor A + aplicação em servidor B, com latência <1 ms.
- Sem cobrança de tráfego interno.

Como pedir: abra ticket com a lista de VMIDs a serem agrupados. Você recebe:
- Uma VLAN dedicada ou bridge interna.
- IPs privados (ex.: `10.50.0.0/24`) para cada servidor.

## 5. Firewall do Proxmox

O painel do cliente permite abrir/fechar portas no **firewall do nó** (nível hipervisor, antes do SO da sua VM). Isso é muito útil para:

- Bloquear portas que você sabe que NÃO precisa (reduz superfície de ataque).
- Liberar serviços específicos (RDP 3389, SIP 5060, MySQL 3306).

### Regras recomendadas (baseline)

| Direção | Origem | Destino | Porta | Ação | Motivo |
|---|---|---|---|---|---|
| IN | qualquer | sua VM | **2222** | ACCEPT | SSH NTWeb |
| IN | qualquer | sua VM | **80, 443** | ACCEPT | Web |
| IN | seu IP fixo | sua VM | qualquer | ACCEPT | Acesso admin |
| IN | qualquer | sua VM | 22 | DROP | SSH default (não usado) |

> Atenção: firewall do Proxmox **não substitui** o firewall do SO. Rode os dois (defesa em camadas).

## 6. Firewall do SO

Debian/Ubuntu — **UFW**:
```bash
ufw default deny incoming
ufw default allow outgoing
ufw allow 2222/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw enable
```

AlmaLinux/Rocky — **firewalld**:
```bash
firewall-cmd --permanent --add-port=2222/tcp
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```

## 7. rDNS (PTR)

Importante para e-mail (SMTP) e para servidores públicos. Sem rDNS batendo com o hostname, vários destinatários vão rejeitar.

- Definir via ticket informando IP + hostname desejado.
- Prazo de propagação: até 24h.

## 8. IP fixo VPN / VLAN WAN

Se você precisa que seu servidor NTWeb fale com um serviço que exige **IP de origem em whitelist**, pode:
- Contratar produto **IP Dedicado** (pasta `ip-dedicado/`).
- Pedir que seu servidor fale via VPN com um IP fixo externo.

## 9. FAQ

- **P:** IP muda se eu reiniciar ou reinstalar?  
  **R:** Não. IPv4 público é **fixo** e ligado ao VMID.
- **P:** CGNAT?  
  **R:** Não usamos CGNAT nos produtos Proxmox. IP é público de verdade.
- **P:** Posso usar DDoS mitigation?  
  **R:** TODO — política depende do plano e do tipo de ataque.

## 10. Quando abrir ticket

- Pedir IP adicional, bridge privada ou VLAN.
- Pedir rDNS / PTR.
- Tráfego anormal (ataque, scan constante, alto uso de banda).
- Regras do firewall do Proxmox não aplicam após salvar.