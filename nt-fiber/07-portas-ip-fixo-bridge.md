---
title: "Port forwarding, IP fixo e modo bridge no NT Fiber (PJ)"
description: "Abra portas para câmeras, DVR, VPN e servidores internos. IP fixo só em planos NT Fiber Pro. Modo bridge entrega PPPoE ao seu roteador (Mikrotik, Unifi, pfSense)."
slug: "portas-ip-fixo-bridge"
category: "NT Fiber"
tags: ["port forwarding", "ip fixo", "bridge", "pppoe", "cgnat", "nat", "cftv"]
author:
  name: "Equipe NTWeb"
  role: "Equipe NTWeb NT Fiber"
  url: "https://ntweb.com.br/sobre"
publisher:
  name: "NTWeb"
  url: "https://ntweb.com.br"
published: "2026-04-20"
modified: "2026-04-21"
canonical: "https://ntweb.com.br/ajuda/nt-fiber/portas-ip-fixo-bridge"
audience: "PJ técnico"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/portas-ip-fixo-bridge.jpg"
---

# Liberar portas, IP fixo e modo bridge (PJ)

> **Resposta rápida (40–60 palavras):**  
> **Abra portas** (câmeras, DVR, VPN, servidor interno) direto no Painel NTWeb na seção **NAT / Virtual Server** do equipamento. **IP fixo** é exclusivo dos planos **NT Fiber Pro** — contrate via ticket Comercial. **Modo bridge** desliga o roteamento do CPE e entrega a conexão PPPoE para o seu próprio roteador (Mikrotik, Unifi, pfSense) — solicitado por ticket técnico.

> **Público:** PJ técnico · **Leitura:** 5 min · **Dependência:** fluxo do Painel em revisão conforme o módulo de gestão de roteador NTWeb.

> Todos os exemplos dependem do painel NTWeb real. Ajustar depois da documentação do módulo.

## 1. Quando cada recurso é necessário

| Cenário | Recurso |
|---|---|
| Acessar câmeras IP/DVR fora de casa | **Port forwarding** + **DDNS** ou **IP fixo** |
| Hospedar serviço (HTTP/SSH/VPN) | **IP fixo** (externo) + **port forward** |
| Usar roteador próprio (Mikrotik, Unifi, pfSense) | **Modo bridge** |
| Montar VPN ponto-a-ponto | **IP fixo** externo |

## 2. Como pedir IP fixo no plano NT Fiber Pro

- Enviar ticket financeiro-comercial informando CNPJ e porta desejada.
- Adicional mensal: TODO.
- Prazo de provisionamento: TODO dias úteis.

> Alternativa: produtos **IP Móvel / IP Dedicado** (grupo separado) via PUQ Mikrotik VPN.

## 3. Port forwarding pelo painel NTWeb

TODO — fluxo real:
- Selecionar o equipamento.
- Menu **NAT / Virtual Server** ou similar.
- Campos: IP interno, porta interna, porta externa, protocolo (TCP/UDP).
- Salvar e validar com `canyouseeme.org` ou `nmap`.

## 4. Alternar para modo bridge

TODO — explicar onde fica o toggle no painel, consequência (Wi-Fi do CPE desliga, PPPoE passa para o roteador do cliente), credenciais PPPoE fornecidas ou não.

## 5. FAQ técnico

- **P:** O IP externo muda sozinho?  
  **R:** Em planos flex, sim (IP dinâmico CGNAT TODO). Em Pro com IP fixo contratado, não.
- **P:** CGNAT no NT Fiber flex?  
  **R:** TODO — confirmar.
- **P:** Quais portas são bloqueadas por padrão?  
  **R:** TODO (listar 25, 465, 110 etc., se houver).

## 6. Quando abrir ticket

- Porta aberta no painel mas o serviço externo não responde.
- Modo bridge aparentemente ativo mas o roteador do cliente não discagem PPPoE.