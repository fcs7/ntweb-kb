---
title: "Métricas, logs e alertas no NT Datacenter — monitore sua VM Proxmox"
description: "Veja CPU, RAM e rede direto no Painel do Cliente (granularidade ~1 min). Para histórico longo e alertas, rode Zabbix, Prometheus+Grafana ou Netdata num LXC."
slug: "metricas-logs-alertas"
category: "NT Datacenter"
tags: ["monitoramento", "métricas", "logs", "alertas", "zabbix", "prometheus", "grafana", "netdata"]
author:
  name: "Equipe NTWeb"
  role: "Engenharia de Infraestrutura"
  url: "https://ntweb.com.br/sobre"
publisher:
  name: "NTWeb"
  url: "https://ntweb.com.br"
published: "2026-04-20"
modified: "2026-04-21"
canonical: "https://ntweb.com.br/ajuda/nt-datacenter/metricas-logs-alertas"
audience: "PJ técnico"
status: "draft"
image: "https://ntweb.com.br/ajuda/_midia/metricas-logs-alertas.jpg"
references:
  - "https://www.zabbix.com/documentation/current/"
  - "https://prometheus.io/docs/introduction/overview/"
  - "https://grafana.com/docs/grafana/latest/"
  - "https://learn.netdata.cloud/"
---

# Métricas, logs e alertas

> **Resposta rápida (40–60 palavras):**  
> **Consulte métricas básicas** (CPU, RAM, disco, rede) no Painel do Cliente → seu servidor → aba Graphs — granularidade de cerca de 1 minuto. **Para histórico longo e alertas**, suba **Zabbix** (template 5106), **Prometheus + Grafana** ou **Netdata** num LXC 2 GB. Logs do SO ficam em `journalctl -u <serviço>` e em `/var/log/`. A NTWeb monitora o nó Proxmox; o que roda dentro da VM é do cliente.

> **Público:** PJ (técnico) · **Leitura:** 6 min

> Três perguntas que todo operador se faz: **"Como está agora?"** (métricas), **"O que aconteceu naquela hora?"** (logs), **"Como eu fico sabendo sem olhar?"** (alertas).

## 1. Métricas — o que a NTWeb entrega de graça

### Pelo Painel do Cliente

1. **Meus Serviços → seu servidor → aba Graphs / Estatísticas.**
2. Você vê séries temporais (última hora, dia, semana, mês) de:
   - CPU (%).
   - RAM (MB usado vs. total).
   - Disco (I/O read/write).
   - Rede (Mbps in/out).

Limitação: granularidade depende da agregação do Proxmox (~1 min).

### Dentro da VM/container (métricas de SO)

Comandos rápidos no SSH:
```bash
htop              # CPU + RAM + processos (interativo)
free -h           # RAM
df -h             # Disco
iostat -xm 2      # I/O de disco (pacote sysstat)
iftop -i eth0     # tráfego de rede em tempo real
```

## 2. Métricas avançadas — seu próprio stack

Para quem quer monitoramento de longo prazo, histogramas, dashboards:

### Opção 1 — Zabbix (NTWeb já tem template)
- Contrate um **NT Cloud LXC 4 GB** ou use o **template 5106** (Zabbix pronto).
- Aponte os agentes Zabbix das suas outras VMs para esse servidor.
- Admin / zabbix inicial — troque no primeiro login.

### Opção 2 — Prometheus + Grafana
- Monte em um LXC 2 GB.
- Instale `node_exporter` em cada VM/container.
- Dashboards prontos em `grafana.com/dashboards` (busque "Node Exporter Full").

### Opção 3 — Netdata (mais simples)
- Agente leve com dashboard pronto em `http://IP:19999`.
- Instalação: `bash <(curl -Ss https://my-netdata.io/kickstart.sh)`.

## 3. Logs — onde estão no Linux

```bash
journalctl -u <serviço> -f             # seguir logs de um serviço
journalctl --since "1 hour ago"
tail -F /var/log/syslog                # Debian/Ubuntu
tail -F /var/log/messages              # AlmaLinux/Rocky
tail -F /var/log/nginx/access.log
```

### Apps comuns:

| App | Caminho |
|---|---|
| SSH | `/var/log/auth.log` (Debian) ou `/var/log/secure` (RHEL) |
| Nginx | `/var/log/nginx/{access,error}.log` |
| Apache | `/var/log/apache2/*` ou `/var/log/httpd/*` |
| MariaDB/MySQL | `/var/log/mysql/error.log` |
| PHP-FPM | `/var/log/php*-fpm.log` |
| Docker (todos) | `docker logs <container>` ou `journalctl -u docker` |

## 4. Logs fora do servidor (centralização)

Se você tem 3+ servidores, vale a pena centralizar:
- **rsyslog** → servidor central de logs.
- **Promtail + Loki** → compatível com Grafana.
- **Elastic / OpenSearch** → pesado, poderoso, indicado só se tiver volume alto.

## 5. Alertas

### Alerta básico — e-mail quando o servidor não responde
Use o **UptimeRobot** (grátis até 50 monitores) ou **BetterStack** / **HealthChecks.io**.

### Alerta específico de métrica
- Em **Zabbix**: triggers.
- Em **Prometheus**: Alertmanager + regras YAML.
- Em **Netdata**: alertas embutidos (configuração em `/etc/netdata/health.d/*.conf`).

### Alerta próprio em shell (quick & dirty)
```bash
# /etc/cron.d/alerta-disco
*/15 * * * * root df / | awk 'NR==2 && $5+0>85 {print "Disco em "$5" no '$HOSTNAME'"}' | mail -s "ALERTA" voce@seudominio.com
```

## 6. O que monitorar no mínimo

- [ ] **Ping/TCP 2222** — servidor está online.
- [ ] **CPU** — sustentado >80% por 10 min = investigar.
- [ ] **RAM** — `MemAvailable` abaixo de 10% = risco de OOM.
- [ ] **Disco** — >85% = alerta. >95% = crítico.
- [ ] **Serviço principal** (HTTP, nginx, php-fpm, mysql).
- [ ] **Certificado SSL** vencendo em 14 dias.

## 7. Suporte da NTWeb

- A NTWeb monitora **saúde do nó Proxmox** (host). Se um nó cai, vamos migrar sua VM automaticamente (em NT Cloud) ou avisar (em NT VPS).
- A NTWeb **não monitora** o que roda dentro do SO da sua VM — isso é responsabilidade do cliente.
- Exceção: produtos **gerenciados** (quando contratados) — escopo definido no contrato.

## 8. FAQ

- **P:** Posso receber notificação do Painel do Cliente por e-mail/SMS?  
  **R:** TODO — confirmar se o módulo ModulesGarden entrega esse tipo de alerta.
- **P:** Logs do Proxmox aparecem para o cliente?  
  **R:** Não. Logs do hipervisor só para o time NTWeb.
- **P:** Posso ter backup de logs para auditoria?  
  **R:** Sim. Centralize em outro servidor seu e configure retenção.

## 9. Quando abrir ticket

- Seu monitor externo marca offline mas o painel mostra "Running".
- CPU constantemente em 100% e você **não consegue identificar o processo**.
- Gráficos do Painel do Cliente pararam de atualizar.