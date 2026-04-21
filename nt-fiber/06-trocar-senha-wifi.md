---
title: "Como trocar a senha do Wi-Fi no NT Fiber"
description: "Troque a senha do Wi-Fi NT Fiber pelo Painel NTWeb (aplica em segundos) ou no IP do roteador em emergência. Use 12+ caracteres com letras, números e símbolos."
slug: "trocar-senha-wifi"
category: "NT Fiber"
tags: ["wifi", "senha", "ssid", "wpa2", "segurança", "cpe"]
author:
  name: "Equipe NTWeb"
  role: "Equipe NTWeb NT Fiber"
  url: "https://ntweb.com.br/sobre"
publisher:
  name: "NTWeb"
  url: "https://ntweb.com.br"
published: "2026-04-20"
modified: "2026-04-21"
canonical: "https://ntweb.com.br/ajuda/nt-fiber/trocar-senha-wifi"
audience: "Misto (PF + PJ)"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/trocar-senha-wifi.jpg"
---

# Trocar a senha do Wi-Fi

> **Resposta rápida (40–60 palavras):**  
> Troque a senha do Wi-Fi do NT Fiber pelo **Painel NTWeb** (recomendado — aplica em segundos e vale como registro oficial) ou diretamente no IP do roteador em caso de emergência. Use no mínimo **12 caracteres** misturando letras, números e símbolos, e evite a senha padrão da etiqueta. Depois de trocar, todos os dispositivos caem e precisam reconectar.

> **Status:** Draft v0.1 — **DEPENDENTE do módulo de gestão de roteador**  
> **Público:** PF + PJ

> ⚠️ A forma oficial de trocar senha será pelo **módulo de gestão de roteador da NTWeb**. Este draft cobre genericamente até termos a documentação.

## 1. Duas formas de trocar

### Forma recomendada: pelo painel NTWeb
TODO — descrever o passo a passo real quando o módulo estiver mapeado:
- Onde o cliente loga.
- Como localiza o equipamento.
- Botão "Alterar Wi-Fi" / "Alterar SSID" / "Alterar Senha".
- Quanto tempo leva para aplicar.

### Forma direta: pelo IP do roteador (não recomendado)
Só em casos onde o painel está fora ou não há acesso.
- IP padrão LAN: TODO (ex.: `192.168.1.1`).
- Usuário e senha padrão: TODO.
- Menu: **Wireless → Security → Passphrase**.
- Salvar e reiniciar o roteador.

## 2. Boas práticas de senha

- Mínimo 12 caracteres, mistura de letras, números e símbolos.
- Evite a senha default de fábrica (qualquer um com a etiqueta pode copiar).
- Separe rede convidados da rede principal (se o CPE permitir).

## 3. Depois de trocar

1. Todos os dispositivos da casa vão **cair**. É esperado.
2. Reconecte cada um usando a nova senha.
3. Se usar TVs e câmeras IP, tenha paciência — às vezes exigem "Esquecer a rede" antes de aceitar a nova senha.

## 4. FAQ

- **P:** Esqueci a senha nova que eu acabei de criar.  
  **R:** Entre no painel NTWeb e troque novamente. A senha antiga não volta.
- **P:** Posso ter SSIDs diferentes para 2,4 GHz e 5 GHz?  
  **R:** TODO.

## 5. Quando abrir ticket

- Painel NTWeb não aceita a troca.
- Depois da troca, alguns dispositivos não reconectam mesmo com "Esquecer a rede".