---
title: "Como usar o {Produto}"
description: "Resposta em 1 frase (até 155 caracteres) descrevendo o que o leitor aprende neste artigo."
slug: "como-usar-o-produto"
category: "{NT Fiber | NT Datacenter | NT VPS | NTFONE | ...}"
product: "{nome exato do SKU no WHMCS}"
author:
  name: "Equipe NTWeb"
  role: "Suporte Técnico NTWeb"
  url: "https://ntweb.com.br/sobre"
published: "2026-04-20"
modified: "2026-04-20"
canonical: "https://ntweb.com.br/ajuda/{categoria}/{slug}"
audience: "Misto (PF + PJ)"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/{slug}.jpg"
---

# Como usar o {Produto}

> **Resposta rápida (40–60 palavras):**  
> TODO — responda a pergunta-título em 2 a 3 frases objetivas, começando pelo verbo de ação. Esse bloco é o que o Google usa como *featured snippet* e o que LLMs (ChatGPT, Perplexity, Google AI Overviews) copiam para citar. Evite jargão, inclua o nome do produto, termine com o próximo passo.

---

## 1. O que é e para quem serve

**{Produto} é** (definição direta em uma frase, padrão "X é Y"). TODO: mais 1–2 parágrafos curtos explicando o benefício antes da ficha técnica.

## 2. Como contratar

- **Link direto no WHMCS:** TODO
- **Prazo de ativação:** TODO
- **Formas de pagamento aceitas:** Pix, boleto, cartão recorrente.

## 3. Primeiro acesso

### 3.1 Para clientes PF (leigos)
TODO — passo a passo com imagem a cada ação.

### 3.2 Para clientes PJ (técnicos)
TODO — credenciais, portas, APIs, automação.

## 4. Tarefas comuns

- [ ] TODO 1
- [ ] TODO 2
- [ ] TODO 3

## 5. Vídeo curto (≤ 3 min)

`[embed placeholder — YouTube/Vimeo]`

## 6. Perguntas frequentes (FAQ)

### O que é {Produto}?
Resposta objetiva em 2–3 frases. Começar pelo verbo.

### Quanto custa o {Produto}?
Resposta objetiva com a faixa de preço ou o link para a tabela atualizada.

### Preciso de {pré-requisito}?
Resposta objetiva.

### Como cancelo o {Produto}?
Resposta objetiva + link para artigo de cancelamento.

## 7. Quando abrir ticket

TODO — critérios objetivos para o cliente saber quando pedir ajuda ao suporte.

## 8. Artigos relacionados

- TODO
- TODO

---

<details>
<summary>Metadados estruturados (JSON-LD)</summary>

Inserir no `<head>` da página publicada no site (gerador automático pode ler do front-matter):

```json
{
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "Article",
      "headline": "{title}",
      "description": "{description}",
      "datePublished": "{published}",
      "dateModified": "{modified}",
      "author": {
        "@type": "Organization",
        "name": "NTWeb",
        "url": "https://ntweb.com.br"
      },
      "publisher": {
        "@type": "Organization",
        "name": "NTWeb",
        "logo": {
          "@type": "ImageObject",
          "url": "https://ntweb.com.br/logo.png"
        }
      },
      "mainEntityOfPage": "{canonical}",
      "image": "{image}",
      "inLanguage": "pt-BR"
    },
    {
      "@type": "FAQPage",
      "mainEntity": [
        { "@type": "Question",
          "name": "O que é {Produto}?",
          "acceptedAnswer": { "@type": "Answer", "text": "..." } },
        { "@type": "Question",
          "name": "Quanto custa o {Produto}?",
          "acceptedAnswer": { "@type": "Answer", "text": "..." } }
      ]
    },
    {
      "@type": "BreadcrumbList",
      "itemListElement": [
        { "@type": "ListItem", "position": 1, "name": "Ajuda", "item": "https://ntweb.com.br/ajuda" },
        { "@type": "ListItem", "position": 2, "name": "{categoria}", "item": "https://ntweb.com.br/ajuda/{categoria}" },
        { "@type": "ListItem", "position": 3, "name": "{title}" }
      ]
    }
  ]
}
```
</details>
