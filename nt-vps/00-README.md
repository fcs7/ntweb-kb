---
title: "NT VPS — KVM e LXC no Proxmox VE (linha VPS padrão)"
description: "NT VPS é a linha VPS padrão da NTWeb sobre Proxmox VE. NT KVM roda Windows e Linux até 64 GB. NT LXC é container Linux até 4 GB, a partir de R$ 29,90/mês."
slug: "nt-vps"
category: "NT VPS"
author:
  name: "Equipe NTWeb"
  role: "Suporte Técnico NTWeb"
  url: "https://ntweb.com.br/sobre"
published: "2026-04-20"
modified: "2026-04-20"
canonical: "https://ntweb.com.br/ajuda/nt-vps/nt-vps-kvm-e-lxc-proxmox-ve"
audience: "Misto"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/nt-vps-kvm-e-lxc-proxmox-ve.jpg"
---

# NT VPS — KVM e LXC (Proxmox VE)

> **Resposta rápida (40–60 palavras):**  
> **NT VPS é a linha VPS padrão da NTWeb**, sobre Proxmox VE 8.x. Cobre dois produtos: **NT KVM** (virtualização completa, Windows ou Linux, até 64 GB de RAM) e **NT LXC** (container Linux até 4 GB, a partir de **R$ 29,90/mês**). Para cargas críticas sem oversell, veja a linha premium [`nt-datacenter`](../nt-datacenter/00-README.md).

Produtos ativos:
- **KVM** — VPS com virtualização completa, ideal para Windows, Docker com kernel próprio, appliances.
- **LXC** — container Linux leve, ideal para Linux puro, serviços web, bancos, filas.

Módulo WHMCS: *Proxmox VE VPS & Cloud* (mesmo da linha NT Cloud)

## Roteiro de artigos

| Nº | Artigo | Público |
|---|---|---|
| 01 | Quando escolher NT VPS (em vez da linha NT Cloud) | PJ |
| 02 | Primeiro acesso ao Painel e à VM/container | PJ |
| 03 | KVM vs LXC: checklist de decisão | PJ |
| 04 | Reinstalar SO e escolher template | PJ |
| 05 | SSH / RDP / noVNC | PJ |
| 06 | Firewall, portas e redes | PJ |
| 07 | Snapshots e backup | PJ |
| 08 | Monitoramento básico (CPU/RAM/disco) | PJ |
| 09 | Upgrade / downgrade e mudança entre KVM e LXC | PJ |

## Conteúdo compartilhado com `nt-datacenter/`

Vários artigos podem ser **únicos** e referenciados por ambos os grupos (evitar duplicação). Próximo passo: confirmar com o dono se o posicionamento comercial justifica conteúdos separados ou se vamos unificar.