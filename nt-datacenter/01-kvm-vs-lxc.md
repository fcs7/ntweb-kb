---
title: "KVM vs LXC: quando escolher cada virtualização no Proxmox VE"
description: "KVM virtualiza hardware completo e roda qualquer SO. LXC compartilha o kernel do host e é cerca de 10x mais leve. Guia prático pra decidir em 2026."
slug: "kvm-vs-lxc"
category: "NT Datacenter"
tags: ["virtualização", "proxmox", "kvm", "lxc", "container", "hypervisor"]
author:
  name: "Equipe NTWeb"
  role: "Engenharia de Infraestrutura"
  url: "https://ntweb.com.br/sobre"
publisher:
  name: "NTWeb"
  url: "https://ntweb.com.br"
published: "2026-04-20"
modified: "2026-04-21"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter/kvm-vs-lxc"
audience: "PJ técnico"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/kvm-vs-lxc.jpg"
reading_time: "6 min"
references:
  - "https://pve.proxmox.com/wiki/Linux_Container"
  - "https://pve.proxmox.com/wiki/Qemu/KVM_Virtual_Machines"
  - "https://linuxcontainers.org/lxc/introduction/"
  - "https://www.linux-kvm.org/page/Main_Page"
---

# KVM vs LXC: qual virtualização escolher?

> **Resposta rápida (40–60 palavras):**  
> **Escolha KVM** pra rodar Windows, BSD, appliances (pfSense, Mikrotik CHR) ou quando precisar de mais de 4 GB de RAM. **Escolha LXC** pra apps Linux até 4 GB — boota em 1–3 segundos, consome 20–50 MB de RAM só pra existir e custa R$ 12,36 por GB (custo NTWeb). LXC compartilha o kernel do host; KVM virtualiza o hardware inteiro.

> **Público:** PJ (técnico) · **Leitura:** 6 min  
> Se o tema é novo pra você, comece pelo [`_intro-virtualizacao-e-containers`](./_intro-virtualizacao-e-containers.md) — usa a analogia de apartamento vs coworking pra explicar a diferença.

## 1. O que são KVM e LXC

**KVM (Kernel-based Virtual Machine)** é um módulo do kernel Linux que transforma qualquer Linux em hypervisor. Cada VM KVM tem seu próprio kernel, roda qualquer sistema operacional (Linux, Windows, BSD) e isola o hardware via QEMU. KVM foi incorporado ao kernel mainline em 2007 (versão 2.6.20) e é mantido pela comunidade em [linux-kvm.org](https://www.linux-kvm.org/page/Main_Page).

**LXC (Linux Containers)** é virtualização em nível de sistema operacional. Todos os containers LXC do mesmo host compartilham o kernel — o isolamento vem de *cgroups* e *namespaces*, primitivas já presentes no kernel Linux. O projeto oficial é o [linuxcontainers.org](https://linuxcontainers.org/lxc/introduction/). O Proxmox VE integrou LXC na versão 4.0, em 2015, substituindo o antigo OpenVZ ([release notes](https://pve.proxmox.com/wiki/Roadmap#Proxmox_VE_4.0)).

Aqui na NTWeb rodamos os dois lado a lado em clusters Proxmox VE dedicados. Essa é a primeira decisão que a gente precisa tomar toda vez que alguém contrata uma máquina.

## 2. Matriz de decisão em 10 segundos

| Cenário | Escolha |
|---|---|
| Rodar Windows, pfSense, FreeBSD, appliance de rede | **KVM** |
| Precisar de kernel próprio (cgroups v2 customizado, k3s, Wireguard em módulo não-mainline) | **KVM** |
| Site, API, banco (MySQL, Postgres, Redis), Docker padrão | **LXC** |
| Laboratório, homelab, app pequeno | **LXC** |
| SLA rígido com isolamento forte | **KVM** |
| Até 4 GB de RAM | **LXC** (NT VPS LXC) |
| Acima de 4 GB de RAM | **KVM** |

## 3. Diferenças operacionais concretas

| Aspecto | KVM | LXC |
|---|---|---|
| Tempo médio de boot | 30–60 s | 1–3 s |
| Overhead de RAM só pra rodar o SO | 200–400 MB | 20–50 MB |
| Densidade típica em host de 128 GB | 20–30 VMs | 80–120 containers |
| Snapshot ao vivo | Sim | Só com ZFS ou qcow2 |
| Live migration entre nós | Sim | Não (stop → migrate → start) |
| Trocar o kernel | Sim | Não (usa o kernel do host) |
| Módulos kernel exóticos | Sim | Não |
| Custo NTWeb por GB de RAM | R$ 18,54 | R$ 12,36 |

Traduzindo os números: LXC tem **cerca de 10x menos overhead de RAM** e **20x menos tempo de boot**. Na densidade de 100 containers por nó que a gente opera em hosts de 128 GB, isso significa quase 1 GB a mais disponível pra aplicação em cada GB vendido — em vez de ser consumido pelo SO da VM.

## 4. Regras da casa

- **NT VPS LXC:** teto de **4 GB de RAM**. Passou disso, o cliente migra pra NT VPS KVM ou NT Cloud KVM.
- **Docker em LXC:** roda com a flag `nesting=1` no container. Nossos templates já trazem ligado.
- **Windows:** sempre em KVM. Sem exceção — LXC só roda Linux.
- **Kubernetes produtivo** (não kind/minikube): KVM, pelo isolamento do kernel.
- **Oversell de RAM:** permitido em LXC com fator 1,5x; proibido em KVM.

## 5. Casos reais

- **Loja Magento ou WooCommerce até ~5 mil SKUs:** LXC 2 GB resolve. R$ 49,90/mês.
- **n8n com 3 integrações leves:** LXC 2 GB usando o app template 5101.
- **Zabbix monitorando até 50 hosts:** LXC 4 GB (template 5106) dá conta. Acima de 150 hosts, pula pra KVM 4 GB por causa do *fsync* mais agressivo do agente.
- **FreePBX em produção:** sempre KVM. Módulos de áudio (`dahdi`, `chan_dahdi`) exigem kernel próprio.
- **Servidor de câmeras IP ou NVR:** KVM, por causa de drivers exóticos e *passthrough*.
- **Postgres dedicado:** LXC até 4 GB. Acima disso, KVM pela previsibilidade de I/O.

## 6. Posso trocar depois?

**De LXC pra KVM:** exige reinstalar e restaurar dados — não existe conversão direta. Antes de mudar, tire snapshot do LXC e exporte o que importa (dump do banco, tarball do `/var/www`, configs).

**Dentro do mesmo tipo:** upgrade de CPU, RAM ou disco é online e sem downtime. Detalhes no [`07-upgrade-recursos`](./07-upgrade-recursos.md).

## 7. Perguntas frequentes

### KVM é mais seguro que LXC?

Isolamento sim. KVM virtualiza CPU, memória e dispositivos em anéis de hardware (VT-x da Intel ou AMD-V), enquanto LXC depende de *namespaces* do kernel compartilhado. Pra ambientes regulados — PCI-DSS, LGPD crítica, dados sensíveis de terceiros — prefira KVM. Pra apps comuns com kernel bem atualizado, LXC é seguro o bastante.

### Posso rodar Docker dentro de LXC?

Pode. Precisa da flag `nesting=1` no container e de montar `/dev/kmsg`. Nossos templates já fazem isso. Tem uma penalidade de 5% a 10% em I/O por causa do duplo *cgroup*, mas em aplicação web é imperceptível.

### Qual a diferença entre LXC e Docker?

LXC é um **container de sistema**: roda `systemd`, múltiplos processos, se comporta como uma VM leve. Docker é um **container de aplicação**: um processo por container, imutável, pensado pra orquestração. Você roda Docker *dentro* de um LXC — não o contrário.

### Quanto custa cada um na NTWeb?

**NT VPS LXC:** 1 GB R$ 29,90 · 2 GB R$ 49,90 · 4 GB R$ 79,90.  
**NT VPS KVM:** 4 GB R$ 129,90 · 8 GB R$ 249,90 · 16 GB R$ 499,90 · 32 GB R$ 999,90 · 64 GB R$ 1.999,90.

A linha NT Cloud (alta disponibilidade e recursos dedicados) tem cotação separada — abra ticket pra proposta.

### LXC tem alguma limitação de segurança conhecida?

Tem. Como o kernel é compartilhado, uma vulnerabilidade local de *privilege escalation* no kernel do host afeta todos os containers. Por isso a gente mantém o kernel do host atualizado e usa LXC **unprivileged** por padrão (UID mapping, usuário não-root no host). Detalhes em [Proxmox Wiki — Unprivileged LXC](https://pve.proxmox.com/wiki/Unprivileged_LXC_containers).

## 8. Quando abrir ticket

- Dúvida específica sobre compatibilidade do seu app.
- Precisa de template customizado (ex.: AlmaLinux 9 em LXC — hoje só temos em KVM).
- Quer migrar um workload existente de outro provedor e não sabe qual tipo pedir.
- App cita "requer kernel X.Y" e você não sabe se o kernel do nosso host atende.

## 9. Leitura relacionada

- [`_intro-virtualizacao-e-containers`](./_intro-virtualizacao-e-containers.md) — analogia simples pra quem tá começando.
- [`02-primeiro-acesso-proxmox`](./02-primeiro-acesso-proxmox.md) — como logar na VM depois de ativada.
- [`03-reinstalar-os-e-templates`](./03-reinstalar-os-e-templates.md) — base templates e app templates disponíveis.
- [`09-nt-datacenter-vs-nt-vps`](./09-nt-datacenter-vs-nt-vps.md) — diferença entre as linhas comerciais.

---

<details>
<summary>Metadados estruturados (JSON-LD)</summary>

```json
{
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "TechArticle",
      "headline": "KVM vs LXC: qual virtualização escolher no Proxmox VE",
      "description": "KVM virtualiza hardware completo e roda qualquer SO. LXC compartilha o kernel do host e é cerca de 10x mais leve. Guia técnico pra decidir em 2026.",
      "datePublished": "2026-04-20",
      "dateModified": "2026-04-21",
      "inLanguage": "pt-BR",
      "author": {
        "@type": "Organization",
        "name": "NTWeb",
        "url": "https://ntweb.com.br"
      },
      "publisher": {
        "@type": "Organization",
        "name": "NTWeb",
        "url": "https://ntweb.com.br",
        "logo": {
          "@type": "ImageObject",
          "url": "https://ntweb.com.br/logo.png"
        }
      },
      "mainEntityOfPage": "https://ntweb.com.br/ajuda/nt-datacenter/kvm-vs-lxc",
      "image": "https://ntweb.com.br/ajuda/_midia/kvm-vs-lxc.jpg",
      "proficiencyLevel": "Expert",
      "about": [
        { "@type": "Thing", "name": "KVM (Kernel-based Virtual Machine)", "sameAs": "https://en.wikipedia.org/wiki/Kernel-based_Virtual_Machine" },
        { "@type": "Thing", "name": "LXC (Linux Containers)", "sameAs": "https://en.wikipedia.org/wiki/LXC" },
        { "@type": "Thing", "name": "Proxmox Virtual Environment", "sameAs": "https://en.wikipedia.org/wiki/Proxmox_Virtual_Environment" }
      ],
      "citation": [
        { "@type": "CreativeWork", "name": "Proxmox VE — Linux Container", "url": "https://pve.proxmox.com/wiki/Linux_Container" },
        { "@type": "CreativeWork", "name": "Proxmox VE — QEMU/KVM Virtual Machines", "url": "https://pve.proxmox.com/wiki/Qemu/KVM_Virtual_Machines" },
        { "@type": "CreativeWork", "name": "LXC Project — Introduction", "url": "https://linuxcontainers.org/lxc/introduction/" },
        { "@type": "CreativeWork", "name": "Linux KVM — Main Page", "url": "https://www.linux-kvm.org/page/Main_Page" },
        { "@type": "CreativeWork", "name": "Proxmox Wiki — Unprivileged LXC", "url": "https://pve.proxmox.com/wiki/Unprivileged_LXC_containers" }
      ]
    },
    {
      "@type": "FAQPage",
      "mainEntity": [
        {
          "@type": "Question",
          "name": "KVM é mais seguro que LXC?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "KVM tem isolamento mais forte porque virtualiza CPU, memória e dispositivos em anéis de hardware (VT-x ou AMD-V). LXC depende de cgroups e namespaces do kernel compartilhado. Para ambientes regulados (PCI-DSS, LGPD crítica) prefira KVM; para apps comuns com kernel atualizado, LXC é seguro o bastante."
          }
        },
        {
          "@type": "Question",
          "name": "Posso rodar Docker dentro de LXC?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "Pode. Precisa da flag nesting=1 no container e de montar /dev/kmsg. Os templates NTWeb já fazem isso. Há penalidade de 5 a 10 por cento em I/O por causa do duplo cgroup, mas em app web é imperceptível."
          }
        },
        {
          "@type": "Question",
          "name": "Qual a diferença entre LXC e Docker?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "LXC é um container de sistema — roda systemd, múltiplos processos, se comporta como uma VM leve. Docker é um container de aplicação — um processo por container, imutável, pensado pra orquestração. Você roda Docker dentro de um LXC, não o contrário."
          }
        },
        {
          "@type": "Question",
          "name": "Quanto custa KVM e LXC na NTWeb?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "NT VPS LXC: 1 GB R$ 29,90, 2 GB R$ 49,90, 4 GB R$ 79,90. NT VPS KVM: 4 GB R$ 129,90, 8 GB R$ 249,90, 16 GB R$ 499,90, 32 GB R$ 999,90, 64 GB R$ 1.999,90. A linha NT Cloud tem cotação separada."
          }
        },
        {
          "@type": "Question",
          "name": "LXC tem alguma limitação de segurança conhecida?",
          "acceptedAnswer": {
            "@type": "Answer",
            "text": "Sim. Como o kernel é compartilhado, uma vulnerabilidade local de privilege escalation no kernel do host afeta todos os containers. Por isso a NTWeb mantém o kernel do host atualizado e usa LXC unprivileged por padrão, com UID mapping e usuário não-root no host."
          }
        }
      ]
    },
    {
      "@type": "BreadcrumbList",
      "itemListElement": [
        { "@type": "ListItem", "position": 1, "name": "Ajuda", "item": "https://ntweb.com.br/ajuda" },
        { "@type": "ListItem", "position": 2, "name": "NT Datacenter", "item": "https://ntweb.com.br/ajuda/nt-datacenter" },
        { "@type": "ListItem", "position": 3, "name": "KVM vs LXC" }
      ]
    }
  ]
}
```
</details>
