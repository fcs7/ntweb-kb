---
title: "NT Datacenter Virtual vs NT VPS — comparativo"
description: "Fluxo curto para decisor:"
slug: "nt-datacenter-virtual-vs-nt-vps-comparativo"
category: "NT Datacenter"
author:
  name: "Equipe NTWeb"
  role: "Suporte Técnico NTWeb"
  url: "https://ntweb.com.br/sobre"
published: "2026-04-20"
modified: "2026-04-20"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter/nt-datacenter-virtual-vs-nt-vps-comparativo"
audience: "Misto"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/nt-datacenter-virtual-vs-nt-vps-comparativo.jpg"
---

# NT Datacenter Virtual vs NT VPS — comparativo

> **Resposta rápida (40–60 palavras):**  
> Escolha a linha **NT Cloud** (Datacenter Virtual) quando o servidor não pode cair: recursos dedicados, zero oversell e migração ao vivo entre nós. Escolha **NT VPS** para dev, staging ou apps pequenos — mesma plataforma Proxmox, mas com oversell de 1,5× em LXC e preço a partir de R$ 29,90/mês. A escolha entre KVM e LXC é uma decisão independente.

> **Status:** Draft v0.1  
> **Público:** PJ (e qualquer decisor comercial)

> Se você ainda está decidindo entre **KVM e LXC**, leia primeiro `_intro-virtualizacao-e-containers.md`. Este artigo compara as **duas linhas comerciais** (Cloud vs VPS) que existem para **cada** tecnologia.

## 1. Os 4 SKUs em 1 tabela

| Produto | Tecnologia | Linha | Posicionamento | Teto de RAM |
|---|---|---|---|---|
| **NT Cloud KVM** | KVM | NT Datacenter Virtual | Cloud premium, HA, recursos dedicados | 64 GB |
| **NT Cloud LXC** | LXC | NT Datacenter Virtual | Cloud premium em container | 64 GB |
| **NT KVM** | KVM | NT VPS | VPS padrão, isolamento total | 64 GB |
| **NT LXC** | LXC | NT VPS | VPS entrada, apps pequenos | **4 GB** |

## 2. Diferença técnica entre as duas linhas

| Aspecto | **NT Cloud** (Datacenter Virtual) | **NT VPS** |
|---|---|---|
| Oversell de RAM | ❌ **Nenhum** — recursos dedicados | ⚠️ LXC: 1,5× / KVM: nenhum |
| Oversell de CPU | ❌ Nenhum | ⚠️ até 3× em LXC (seguro p/ web) |
| Nó físico | Nós "Cloud" (mais RAM livre, redundância) | Nós "VPS" (densidade maior, mais LXCs por nó) |
| Alta disponibilidade | ✅ Migração ao vivo em caso de falha de nó | ⚠️ Reinício em outro nó (alguns minutos de downtime) |
| SLA | Mais apertado (a definir em contrato) | Padrão de VPS |
| Preço | Mais caro | Mais barato |
| Suporte prioritário | ✅ | Padrão |

> **O que o cliente final sente na prática:**
> - **Cloud** = "paguei mais caro e meu servidor praticamente não cai."
> - **VPS** = "preço competitivo, suficiente para a maioria dos apps, mas se o nó cair eu levo uns minutos de downtime."

## 3. Para que serve cada um (roteiro comercial)

### NT Cloud KVM
- E-commerce grande ou com pico de venda (Black Friday).
- Ambiente de produção com SLA formal.
- Windows Server em produção (licenças, ERP, AD).
- Appliances críticos (pfSense core, PBX de empresa).

### NT Cloud LXC
- App Linux crítico (produção) que não precisa de kernel próprio.
- Banco de dados transacional em Linux (Postgres, MariaDB).
- Serviço interno de grande empresa (intranet, CI/CD).

### NT KVM (linha VPS)
- Dev/staging, ambientes de homologação.
- Loja Magento/WooCommerce inicial.
- Lab com Windows, appliance, pfSense de teste.

### NT LXC (linha VPS)
- App pequeno (até 4 GB): site em PHP, n8n, Zabbix de pequeno porte, WordPress com ~10 k visitas/mês.
- Homelab, estudos, bot de Telegram, bot de discord.
- Serviço auxiliar de empresa (cache, fila).

## 4. Tabela de preços atual (referência, pode mudar)

### NT VPS LXC (aprovado em 2026-04-19)

| Plano | RAM | vCPU | Disco | IPv4 | Preço mensal |
|---|---|---|---|---|---|
| LXC-1 | 1 GB | 1 | 20 GB | 1 | **R$ 29,90** |
| LXC-2 | 2 GB | 2 | 40 GB | 1 | **R$ 49,90** |
| LXC-3 | 3 GB | 2 | 60 GB | 1 | **R$ 69,90** |
| LXC-4 | 4 GB | 4 | 80 GB | 1 | **R$ 79,90** |

### NT VPS KVM

| Plano | RAM | Preço mensal |
|---|---|---|
| KVM-4  | 4 GB  | **R$ 129,90** |
| KVM-8  | 8 GB  | **R$ 249,90** |
| KVM-16 | 16 GB | **R$ 499,90** |
| KVM-32 | 32 GB | **R$ 999,90** |
| KVM-64 | 64 GB | **R$ 1.999,90** |

### NT Cloud KVM / NT Cloud LXC
**A definir** — pricing premium em relação ao NT VPS (TODO publicar quando aprovado).

> Todos os preços podem variar. Veja sempre a página oficial do produto no site `ntweb.com.br` para a versão vigente.

## 5. Escolhendo em 30 segundos

Fluxo curto para decisor:

1. **Preciso Windows, >4 GB, ou kernel próprio?**
   - Sim → KVM. Não → LXC.
2. **Esse servidor para a operação se cair por 10 minutos?**
   - Sim → **NT Cloud**.
   - Não → **NT VPS**.

Combine os dois eixos → tem seu SKU.

## 6. Dá pra subir de linha depois?

| Mudança | Como |
|---|---|
| NT VPS KVM → NT Cloud KVM | Migração de nó, janela curta, sem trocar IP |
| NT VPS LXC → NT Cloud LXC | Idem |
| NT VPS LXC → NT KVM (ou Cloud KVM) | **Recriar + migrar dados** (não é upgrade automático) |

## 7. FAQ

- **P:** Posso começar no NT VPS e subir pro NT Cloud só na Black Friday?  
  **R:** Sim, upgrade de linha é possível. Peça com 48 h de antecedência no ticket para garantirmos a janela.
- **P:** Meu cliente exige SLA em contrato. Só NT Cloud?  
  **R:** Sim. NT VPS não vem com SLA formal.
- **P:** Posso ter IPv4 em ambos?  
  **R:** Sim, todos os produtos têm IPv4 dedicado incluso.

## 8. Quando abrir ticket

- Quer uma proposta customizada (ex.: 128 GB — acima do teto) → conversa com a área de **Infraestrutura**.
- Quer migrar de linha sem downtime.
- Precisa de mais de 1 IPv4, rDNS, IPv6 /64.