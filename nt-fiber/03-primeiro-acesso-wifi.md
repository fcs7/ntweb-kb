---
title: "Primeiro acesso ao Wi-Fi do NT Fiber — SSID e senha da etiqueta"
description: "Conecte ao NT Fiber usando o SSID e a senha impressos na etiqueta embaixo do roteador. Troque a senha padrão no Painel NTWeb logo após o primeiro acesso."
slug: "primeiro-acesso-wifi"
category: "NT Fiber"
tags: ["wifi", "primeiro acesso", "ssid", "senha", "onu", "cpe"]
author:
  name: "Equipe NTWeb"
  role: "Equipe NTWeb NT Fiber"
  url: "https://ntweb.com.br/sobre"
publisher:
  name: "NTWeb"
  url: "https://ntweb.com.br"
published: "2026-04-20"
modified: "2026-04-21"
canonical: "https://ntweb.com.br/ajuda/nt-fiber/primeiro-acesso-wifi"
audience: "Misto (PF + PJ)"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/primeiro-acesso-wifi.jpg"
---

# Primeiro acesso ao Wi-Fi — NT Fiber

> **Resposta rápida (40–60 palavras):**  
> Depois da instalação, conecte celular e notebook ao Wi-Fi do NT Fiber usando o **SSID** (nome da rede) e a **senha** que estão impressos na **etiqueta embaixo do roteador**. Respeite maiúsculas e símbolos. Assim que entrar, abra o **Painel do Cliente NTWeb** para trocar a senha padrão e registrar seus dispositivos — segurança básica do dia 1.

> **Público:** PF + PJ · **Leitura:** 3 min · **Dependência:** artigo em revisão após documentação final do módulo de gestão de roteador NTWeb.

> ⚠️ Este artigo será reescrito quando tivermos a documentação do módulo de gestão de roteador da NTWeb. As seções TODO refletem pontos que mudam conforme o fluxo do painel.

---

## 1. O que o cliente recebe ao final da instalação

- ONU/roteador (CPE) em comodato, já energizado pelo técnico.
- Uma etiqueta com **SSID** (nome da rede) e **senha** padrão, normalmente colada embaixo do equipamento.
- Acesso ao **Painel do Cliente NTWeb** (WHMCS) — `area.ntweb.com.br` TODO.
- Acesso ao **Módulo de Gestão de Roteador** — TODO (URL / como entra).

## 2. Conectar um celular/notebook na rede

### Versão PF (passo a passo)
1. No celular, abra **Configurações → Wi-Fi**.
2. Procure o nome da rede (SSID) que está na etiqueta do roteador.
3. Toque na rede e digite a senha exatamente como impressa (cuidado com maiúsculas).
4. Aguarde o ícone de Wi-Fi ficar ativo. Pronto, está conectado.

### Versão PJ
- SSID 2,4 GHz e 5 GHz vêm separados ou unificados? TODO.
- Padrão de segurança: WPA2-PSK TODO.
- MTU/MRU padrão e IP da LAN TODO.

## 3. Entrar no Painel de Gestão de Roteador

TODO — descrever fluxo real assim que o módulo estiver mapeado:
- Login SSO via WHMCS? ou login próprio?
- URL direta ou botão dentro do Painel do Cliente?
- O que é possível fazer (trocar SSID/senha, reiniciar, ver conectados, abrir portas, alternar bridge).

## 4. Vídeo curto (≤ 3 min)

`[embed placeholder]`

## 5. FAQ

- **P:** Esqueci a senha padrão do Wi-Fi. Onde encontro?  
  **R:** TODO (no painel, ou na etiqueta).
- **P:** Posso personalizar o nome da rede?  
  **R:** Sim — veja `06-trocar-senha-wifi.md`.
- **P:** Posso usar meu próprio roteador?  
  **R:** TODO.

## 6. Quando abrir ticket

- Não consegue entrar no painel de gestão.
- A senha da etiqueta não funciona.
- A ONU está piscando vermelho (veja `05-religamento-apos-queda.md` antes).