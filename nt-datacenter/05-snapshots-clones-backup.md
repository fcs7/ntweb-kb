---
title: "Snapshots, clones e backup agendado"
description: "Dentro do host Proxmox acesso só pelo time da NTWeb: qm snapshot <VMID> nome # snapshot KVM qm rollback <VMID> nome pct snapshot <VMID> nome # snapshot LXC"
slug: "snapshots-clones-e-backup-agendado"
category: "NT Datacenter"
author:
  name: "Equipe NTWeb"
  role: "Suporte Técnico NTWeb"
  url: "https://ntweb.com.br/sobre"
published: "2026-04-20"
modified: "2026-04-20"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter/snapshots-clones-e-backup-agendado"
audience: "Misto"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/snapshots-clones-e-backup-agendado.jpg"
---

# Snapshots, clones e backup agendado

> **Resposta rápida (40–60 palavras):**  
> Use **snapshot** para "undo" rápido antes de mudanças arriscadas (mesmo storage, segundos). Use **clone** para duplicar uma VM em ambiente paralelo (mesmo nó, minutos). Use **backup** (Proxmox Backup Server) para sobreviver à perda do nó — o único que protege de verdade. Recomendação: snapshot antes de cada `apt upgrade` e teste restore pelo menos 1× no primeiro mês.

> **Status:** Draft v0.1  
> **Público:** PJ

> Três conceitos parecidos e fáceis de confundir. Resumo: **snapshot** = ponto de restauração rápido no mesmo storage; **clone** = cópia nova e independente; **backup** = arquivo externo para sobrevivência do nó.

## 1. Snapshot

### Quando usar
- Antes de um `apt upgrade` arriscado.
- Antes de mexer em `nginx.conf`, migração de banco, patch de app.
- Testar uma mudança reversível em minutos.

### Como fazer
**Painel do Cliente → serviço → Snapshots → Create.**
Dê um nome descritivo (ex.: `pre-upgrade-php82`). Informe se quer incluir **memória RAM** (só KVM — permite reverter sem reiniciar).

### Limitações
- Armazenados no mesmo storage da VM. Se o nó morrer, o snapshot morre junto.
- **LXC:** só funciona em storage com suporte a snapshot (ZFS, qcow2). Em `local-lvm` puro pode não estar disponível TODO confirmar.
- Snapshots antigos consomem disco — apague os que não precisar.

### Reverter
Snapshots → escolher → **Rollback**. VM reinicia.

## 2. Clone

### Quando usar
- Criar um ambiente de staging idêntico ao produção.
- Duplicar uma VM configurada para entregar a outro cliente/setor.
- Testar uma migração de SO com os dados reais, sem mexer na original.

### Como fazer
**Painel do Cliente → serviço → Clone → Nome da nova VM / VMID destino.**

### Modos
- **Full clone** (padrão): cópia independente, ocupa o mesmo disco.
- **Linked clone** (quando disponível): usa o disco original como base, mais rápido; mas ambos morrem se o disco-base morrer.

### Limitação
- Cloning de **LXC** é suportado mas leva mais tempo.
- IPs / MAC devem ser **trocados** no clone antes de subir (senão dá conflito na rede).

## 3. Backup externo (Proxmox Backup Server)

### O que é
Backup completo e comprimido, gravado **em outro nó de backup** — sobrevive a perda do nó de produção.

### Política da NTWeb (a confirmar)
- Backup diário retido por TODO dias.
- Backup semanal retido por TODO semanas.
- Backup mensal retido por TODO meses.

### Como ver seus backups
Painel do Cliente → serviço → **Backups**. Lista vem direta do Proxmox Backup Server.

### Restaurar
- Selecione o backup → **Restore**.
- Escolha: restaurar **por cima** da VM atual (destrutivo) ou criar **uma nova VM** a partir do backup (seguro, pede VMID livre — avisar suporte).

### Limites
- Retenção acima do padrão: custo adicional (abrir ticket comercial).
- Restore pode levar de 5 min a 2h, depende do tamanho do disco.

## 4. Snapshot vs Clone vs Backup — tabela final

| Característica | Snapshot | Clone | Backup |
|---|---|---|---|
| Finalidade | "undo" rápido | Cópia independente | Sobrevivência do nó |
| Local | Mesmo storage | Mesmo nó | Nó de backup externo |
| Tempo para criar | Segundos | Minutos | Minutos a horas |
| Pode reverter só ele? | Sim | N/A | Sim (restore) |
| Protege contra perda do nó? | ❌ | ❌ | ✅ |
| Custo incremental | Alto | Alto | Gerenciado pelo backup |

## 5. Estratégia recomendada

1. **Snapshot antes de toda mudança arriscada.** Faça e apague quando terminou.
2. **Backup externo agendado** (automático, via política da casa).
3. **Clone manual** quando precisar de ambiente paralelo.
4. **Teste o restore pelo menos 1x** no primeiro mês — backup que nunca foi restaurado é só um arquivo.

## 6. Automação via SSH (cheat sheet para quem gosta)

Dentro do host Proxmox (acesso só pelo time da NTWeb):
```
qm snapshot <VMID> nome       # snapshot KVM
qm rollback <VMID> nome
pct snapshot <VMID> nome      # snapshot LXC
pct rollback <VMID> nome
```
Clientes **não** têm shell no host — use o Painel do Cliente.

## 7. FAQ

- **P:** Quantos snapshots posso manter?  
  **R:** Tecnicamente ilimitado, mas cada um ocupa disco. Recomendado máx. 3 ao vivo.
- **P:** Snapshot tira downtime?  
  **R:** KVM com memória pode precisar de 1–2 s de pausa; sem memória é quase instantâneo. LXC para alguns segundos.
- **P:** Posso migrar backup para outro provedor?  
  **R:** Sim — exporte o `.vma` / `.pbs` e importe no destino. Abra ticket para download.

## 8. Quando abrir ticket

- Restore falhou no meio ou o backup está marcado com erro.
- Backup agendado não está rodando há mais de 48h.
- Preciso de restore para um **VMID diferente** (emergência, produção caiu).