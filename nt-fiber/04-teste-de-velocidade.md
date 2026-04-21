---
title: "Teste de velocidade — como medir certo"
description: "Muita reclamação de 'internet lenta' é, na verdade, limitação do dispositivo, do Wi-Fi ou do servidor acessado — não do link contratado. O teste feito da f"
slug: "teste-de-velocidade-como-medir-certo"
category: "NT Fiber"
author:
  name: "Equipe NTWeb"
  role: "Suporte Técnico NTWeb"
  url: "https://ntweb.com.br/sobre"
published: "2026-04-20"
modified: "2026-04-20"
canonical: "https://ntweb.com.br/ajuda/nt-fiber/teste-de-velocidade-como-medir-certo"
audience: "Misto"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/teste-de-velocidade-como-medir-certo.jpg"
---

# Teste de velocidade — como medir certo

> **Resposta rápida (40–60 palavras):**  
> Meça a velocidade do NT Fiber com o computador conectado por **cabo de rede** (nunca só Wi-Fi), fechando downloads e streaming. Use fast.com, speedtest.net ou medidor.nic.br — rode 3 medições seguidas e tire a média. Resultado acima de **80% do plano contratado** é normal; abaixo de **50% via cabo direto**, abra ticket.

> **Status:** Draft v0.1  
> **Público:** PF + PJ

## 1. Por que o teste "certo" importa

Muita reclamação de "internet lenta" é, na verdade, limitação do dispositivo, do Wi-Fi ou do servidor acessado — não do link contratado. O teste feito da forma correta isola a velocidade real entregue pela NTWeb.

## 2. Como medir (PF)

1. Conecte o **computador direto na porta LAN** do roteador com **cabo de rede** (não confie apenas no Wi-Fi).
2. Feche downloads, streaming, atualizações e videochamadas em todos os aparelhos da casa.
3. Acesse um dos sites de teste:
   - `fast.com` (mais simples)
   - `speedtest.net` (servidor automático)
   - `medidor.nic.br` (referência brasileira)
4. Rode o teste 3 vezes seguidas e use a média.

## 3. Como medir (PJ)

- Use máquina com placa Gigabit e cabo cat.5e/cat.6.
- Para planos **flex 1 Giga** e **Pro 1 GB**: testar com **iperf3** contra um servidor público de 10 Gbps para não ter gargalo do lado remoto.
- Rodar em horários diferentes (dia, noite, madrugada) e comparar.

## 4. Interpretando o resultado

| Medido / Contratado | Leitura |
|---|---|
| ≥ 80% | Dentro do esperado. |
| 50% a 80% | Investigue Wi-Fi, cabo, dispositivo. |
| < 50% via cabo direto | Abrir ticket. |

## 5. FAQ

- **P:** Testei no celular e deu metade. É problema da NTWeb?  
  **R:** Provavelmente Wi-Fi. Refaça com cabo direto no roteador.
- **P:** De madrugada fica rápido e no horário nobre cai. É normal?  
  **R:** Alguma variação é esperada. Se cair abaixo de 80% do plano por mais de 30 min, abra ticket.

## 6. Quando abrir ticket

- Teste com cabo direto mostra menos de 50% do plano contratado, repetido em 3 medições.
- Latência constante acima de 50 ms para sites brasileiros.