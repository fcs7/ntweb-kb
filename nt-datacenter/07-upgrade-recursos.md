---
title: "Upgrade de CPU, RAM e disco sem reinstalar o servidor Proxmox"
description: "No NT Datacenter você faz upgrade de CPU, RAM e disco direto pelo Painel do Cliente, com pagamento pró-rata e sem perder dados ou IP. LXC é online; KVM pode pedir reboot."
slug: "upgrade-recursos"
category: "NT Datacenter"
tags: ["upgrade", "downgrade", "hotplug", "cpu", "ram", "disco", "growpart"]
author:
  name: "Equipe NTWeb"
  role: "Engenharia de Infraestrutura"
  url: "https://ntweb.com.br/sobre"
publisher:
  name: "NTWeb"
  url: "https://ntweb.com.br"
published: "2026-04-20"
modified: "2026-04-21"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter/upgrade-recursos"
audience: "PJ técnico"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/upgrade-recursos.jpg"
references:
  - "https://pve.proxmox.com/wiki/Hotplug_(qemu_disk,nic,cpu,memory)"
  - "https://pve.proxmox.com/wiki/Resize_disks"
---

# Upgrade de recursos (CPU, RAM e disco) sem reinstalar

> **Resposta rápida (40–60 palavras):**  
> Suba CPU, RAM ou disco direto pelo **Painel do Cliente → Upgrade/Downgrade**. O pagamento é **pró-rata** e normalmente leva menos de 1 minuto em planos até 32 GB. No **LXC** a mudança é online; no **KVM** a RAM e o disco podem pedir reboot pro SO reconhecer. Para disco ampliado, rode `growpart` e `resize2fs` dentro da VM.

> **Público:** PJ (técnico) · **Leitura:** 5 min

## 1. O que pode ser feito sem reinstalar

| Recurso | KVM | LXC | Precisa reiniciar? |
|---|---|---|---|
| RAM | ✅ Hotplug ou após reboot | ✅ Online | KVM: reboot recomendado para SO reconhecer |
| vCPU | ✅ Hotplug | ✅ Online | Não (normalmente) |
| Disco (aumentar) | ✅ | ✅ | Não — mas o filesystem precisa ser expandido dentro da VM |
| Disco (diminuir) | ❌ (geralmente inviável) | ⚠️ Com cuidado | — |
| Porta de rede extra | ✅ | ✅ | Reboot para aparecer |

## 2. Como solicitar um upgrade

### Pelo Painel do Cliente
1. **Meus Serviços → NT Cloud/VPS → Upgrade/Downgrade.**
2. Escolha o plano novo.
3. Informe data para aplicar ou escolha "imediato".
4. Pagamento é **pró-rata** (sistema calcula a diferença).

### Por ticket
Quando o painel não oferece o plano desejado, abra ticket no **Comercial** com:
- VMID e plano atual.
- RAM / vCPU / disco que você quer chegar.
- Janela de manutenção preferida.

## 3. O que acontece no momento do upgrade

### KVM
1. Módulo WHMCS chama a API do Proxmox (`qm set <vmid> --memory X --cores Y`).
2. RAM/CPU podem ser **hotplug** se o SO suportar (Linux moderno suporta; Windows requer reboot).
3. Para disco: o .qcow2 é ampliado. Você precisa rodar **dentro da VM**:
   ```bash
   # Ver o novo tamanho do disco virtual
   lsblk
   # Expandir partição (ex.: /dev/sda1)
   growpart /dev/sda 1
   # Expandir filesystem
   resize2fs /dev/sda1   # ext4
   xfs_growfs /           # XFS
   ```

### LXC
1. Módulo chama `pct set <vmid> --memory X --cores Y --rootfs <storage>:<vmid>,size=<novo>G`.
2. Não precisa reboot para RAM/CPU.
3. Disco é expandido online — o container vê imediatamente.

## 4. Upgrade entre planos da casa

### Dentro da mesma linha (LXC → LXC ou KVM → KVM)
- Imediato, sem recriar o servidor.
- Sem perda de dados, sem troca de IP.

### Entre linhas (NT VPS → NT Cloud, mesma tecnologia)
- Em geral: mudança apenas **comercial** (posicionamento / SLA). Técnico continua o mesmo.
- Pode exigir migração de nó (se Cloud e VPS estão em nós separados) — janela pequena.

### De LXC para KVM (ou vice-versa)
- ⚠️ **Não é upgrade**. Precisa contratar o produto novo, migrar dados manualmente e cancelar o antigo.
- Dicas: exporte bancos com `mysqldump`, aplicações via `rsync`, configs com `git` ou ticket de snapshot.

## 5. Downgrade

### É possível em:
- RAM (KVM: reboot necessário. LXC: online).
- vCPU (idem).
- Disco: **tecnicamente sim, mas arriscado**. Exige zerar espaço no fim do disco; inviável se você usa ≥ 80%.

### Regras comerciais
- Dentro da fidelidade, downgrade pode gerar ajuste no contrato.
- Sem fidelidade: pro-rata no mês vigente.

## 6. Estouro de teto

- **NT VPS LXC:** teto de **4 GB** de RAM. Acima disso o sistema **não deixa upgradar** — o pedido é redirecionado para NT VPS KVM ou NT Cloud.
- **Qualquer linha:** teto absoluto da casa é **64 GB** de RAM por produto.

## 7. Erros comuns no pós-upgrade

| Sintoma | Causa | Fix |
|---|---|---|
| `free -h` mostra RAM antiga após upgrade | KVM precisa de reboot | `reboot` |
| `df -h` não mostra disco maior | Filesystem não expandido | `growpart` + `resize2fs`/`xfs_growfs` |
| SSH cai por 10–30s durante upgrade | Migração online | Aguardar; normal |
| Serviços param após upgrade | Sobrecarga momentânea | Logs + `systemctl restart <srv>` |

## 8. FAQ

- **P:** Vou perder IP ao fazer upgrade?  
  **R:** Não. IP é fixo e ligado ao VMID.
- **P:** Vou perder dados?  
  **R:** Não, em upgrade **dentro da mesma linha**. Em migração entre tipos (LXC↔KVM) sim — planeje backup antes.
- **P:** Posso agendar upgrade para 3 da manhã?  
  **R:** Sim, via ticket. Upgrades de até 32 GB normalmente rodam em <1 min.

## 9. Quando abrir ticket

- Upgrade submetido há mais de 10 min e o painel não atualizou.
- Disco foi aumentado mas o `df` não mostra (e `resize2fs` deu erro).
- Precisa janela de manutenção fora do horário comercial.