# Projeto-NOC

> **Investigação, Monitoramento e Observabilidade de Redes**
> Ubuntu Server + Redes + Zabbix + Grafana

## Identificação

| Aluno | Gabriel Henrique |
| Turma | Defesa Cibernética - 2026 |
| Professor | Frank Philson |
| Data | 17/09/2026 |
| Rede do laboratório | 10.110.102.0 |

## Objetivo

Implementar e documentar um laboratório de  **Network Operations Center (NOC)**, capaz de monitorar a disponibilidade, serviços e recursos, combinando diagnóstico de rede, análise de pacotes, monitoramento com Zabbix e visualização no Grafana.

> **Regra operacional utilizada:** primeiro observar e coletar evidências; depois formular a hipótese, corrigir, validar e documentar.

## Ambiente de referência

| Hostname | IP | Função |
|---|---:|---|
| `zabbix-srv-gh` | `10.110.102.132` | Zabbix Server + MariaDB + Frontend |
| `graf-srv-gh` | `10.110.102.110` | Grafana |
| `srv-linux-gh` | `10.110.102.109` | Servidor monitorado |
| Gateway | `10.20.30.1` | Saída da rede do laboratório |

## Sumário

- [Fase 01 — VMs e sistemas operacionais] (#fase-01--vms-e-sistemas-operacionais)
- [Fase 02 — IP estático e conectividade] (#fase-02--ip-estatico-e-conectividade)
- [Fase 03 — Preparação dos servidores] (#fase-03--preparacao-servidores)
- [Fase 04 — Disponibilizar SSH e HTTP] (#fase-04--servicos-ssh-e-http)
- [Fase 05 — Diagnóstico manual e Wireshark] (#fase-05--diagnostico-manual-e-wireshark)
- [Fase 06 — Zabbix Server] (#fase-06--zabbix-server)
- [Fase 07 — Grafana] (#fase-07--grafana)
- [Fase 08 — API Zabbix] (#fase-08--api-zabbix)
- [Fase 09 — Integração Grafana + Zabbix] (#fase-09--integracao-grafana--zabbix)
- [Fase 10 — Dashboard NOC] (#fase-10--dashboard-noc)
- [Fase 11 — Controles de Segurança] (#fase-11--controles-de-seguranca)
- [Fase 12 - Simulação de incidentes] (#fase-12--simulacao-de-incidentes)

---

## Fase 01 — VMs e sistemas operacionais

### Objetivo
Criar as três VMs e instalar o sistema operacional.

A principio, é preciso criar três VMs Ubuntu Server com sua versão mais recente com 4 vCPU, 16 GB de RAM e 100 GB de disco cada. Há a necessidade de três VMs para garantir a segurança e disponibilidade dos serviços.

### Checkpoint
** As 3 VMs inicializadas

### Evidências

![Fase 01 — VMs e sistemas operacionais](imagens/fase01-vms.png)

---

## Fase 02 — IP estático e conectividade

### Objetivo
Configurar IPs estáticos, rota, gateway e DNS.

Os três servidores recebem IP estático e são validados com `ip -br addr`, `ip route`, ping e resolução DNS.

**Comandos/itens de validação:** `ip -br addr` • `ip route` • `ping` • `getent hosts`

### Checkpoint
**As três VMs devem se comunicar e resolver nomes.**

### Evidências

![Fase 02 — IP estático e conectividade](imagens/fase02-conectividade.png)

---

## Fase 03 — Preparação Servidores

### Objetivo
Padronizar hostname, atualizar pacotes e validar horário/NYP.

Os hostnames são padronizados, os pacotes atualizados e o fuso horário definido para o que melhor se encaixa com o local.

**Comandos/itens de validação:** `hostnamectl` • `timedatectl` • `apt update` • `apt upgrade -y`

### Checkpoint
**Hostnames corretos e relógios sincronizados.**

### Evidências

![Fase 03 — Preparação Servidores](imagens/fase03-preparacao-servidores.png)

## Fase 04 — Serviços SSH e HTTP

### Objetivo
Disponibilizar SSH e Apache no srv-linux-gh.
No servidor monitorado(srv-linux-gh), o SSH e Apache são instalados, habilitados e testados local e remotamente.

### Checkpoint
**Portas 22 e 80 acessíveis pela rede do laboratório.**

### Evidências
![Fase 04 — Serviços SSH e HTTP](imagens/fase04-servicos.png)

---
## Fase 05 — Diagnóstico manual e Wireshark

### Objetivo
Registrar o baseline e analisar protocolos antes do monitoramento automático.

É registrado o baseline da rede e capturados ICMP, ARP, DNS, TCP e TLS. O three-way handshake é identificado.

**Comandos/itens de validação:** `icmp` • `arp` • `dns` • `tcp` • `tls`

Essa fase pode ser executada tanto em uma máquina com sistema operacional Windows quanto em uma com o Ubuntu.

### Evidências

![Fase 05 — Diagnóstico manual e Wireshark](imagens/fase05-wireshark.png)

--- 

## Fase 06 — Zabbix Server
### Objetivo
1-Instalar MariaDB, Zabbix Server, frontend e Agent 2 no ZABBIX01;
2-Instalar/configurar Agent 2 e cadastrar srv-linux-gh no Zabbix;
3-Monitorar disponibilidade, serviços e recursos.

### Objetivo 1

No `zabbix-srv-gh`, MariaDB, Zabbix Server, frontend Apache/PHP e Agent 2 são instalados e validados.

**Comandos/itens de validação:** `systemctl status zabbix-server` • `ss -lntp`

### Checkpoint 1
**Frontend funcionando e serviços ativos.**

### Evidências 1

![Fase 06 — Zabbix Server](imagens/fase06-configuracao.png)

### Objetivo 2

O `srv-linux-gh` é cadastrado como host e o Agent 2 passou a enviar métricas para o Zabbix.

**Comandos/itens de validação:** `systemctl status zabbix-agent2` • `Latest data`

### Checkpoint
**Host disponível e enviando métricas.**

### Evidências 2

![Fase 06 — Zabbix Server](imagens/fase06-agent-2.png)

### Objetivo 3

São validados ICMP, HTTP, CPU, memória, disco, rede, uptime e a visão de Problems.

**Comandos/itens de validação:** ICMP • HTTP • CPU • memória • disco • RX/TX • Problems

### Checkpoint
**ICMP, HTTP, CPU, memória, disco, rede e Problems validados.**

### Evidências 3

![Fase 06 — Zabbix Server](imagens/fase06-monitoramento.png)

---

## Fase 07 — Grafana

### Objetivo
Instalar e proteger o Grafana no GRAFANA01.

O Grafana é instalado no `graf-srv-gh` e o acesso ficou restrito à rede do laboratório.

**Comandos/itens de validação:** `systemctl status grafana-server` • porta `3000/TCP`

### Checkpoint
**Grafana ativo e acessível somente pela rede do laboratório.**

### Evidências

![Fase 07 — Grafana](imagens/fase07-grafana.png)

---

## Fase 08 — API Zabbix

### Objetivo
Criar identidade de integração de somente leitura.

É criada a identidade `grafana_ro`, com permissão somente de leitura e token dedicado. O token real não foi publicado.

### Checkpoint
**Usuário e API Token dedicados criados.**

### Evidências

![Fase 08 — API Zabbix](imagens/fase08-api-zabbix.png)

---

## Fase 09 — Integração Grafana + Zabbix

### Objetivo
Instalar plugin Zabbix e criar o data source.

O plugin Zabbix é habilitado e o data source `Zabbix-NOC` retornou `Save & test` com sucesso.

### Checkpoint
**Save & test concluído com sucesso.**

### Evidências

![Fase 09 — Integração Grafana + Zabbix](imagens/fase09-integracao.png)

---

## Fase 10 — Dashboard NOC

### Objetivo
Criar dashboard operacional.

O dashboard reúne disponibilidade dos hosts, CPU, memória, disco, rede, HTTP, uptime e problemas ativos.

### Checkpoint
**Painéis de disponibilidade, CPU, memória, disco, rede, HTTP e problemas.**

### Evidências

![Fase 10 — Dashboard NOC](imagens/fase10-dashboard.png)

---

## Fase 11 — Segurança

### Objetivo
Revisar firewall, SSH, privilégios e exposição de serviços.

As regras de firewall e os privilégios são revisados, evitando exposição desnecessária de serviços e credenciais.

**Comandos/itens de validação:** `sudo ufw status numbered`

### Checkpoint
**Somente acessos necessários devem permanecer liberados.**

### Evidências

![Fase 11 — Segurança](imagens/fase11-seguranca.png)

---

## Fase 12 — Simulação de incidentes

### Objetivo
Provocar falhas controladas e investigar antes de corrigir.

Foi simulado Apache parado. O host permaneceu acessível por ICMP, mas o HTTP falhou; a causa foi confirmada e o serviço restaurado.

**Comandos/itens de validação:** `systemctl` • `journalctl` • `curl` • `ping`

### Checkpoint
**Incidente detectado, diagnosticado, corrigido e validado.**

### Evidências

![Fase 12 — Simulação de incidentes](imagens/fase12-incidentes.png)

---

## Conclusão

O laboratório permitiu acompanhar todo o caminho de uma operação NOC: planejamento, conectividade, diagnóstico de protocolos, implantação do monitoramento, construção de dashboards, aplicação de controles de segurança e investigação de incidentes. O principal aprendizado foi separar **conectividade, serviço e aplicação**: um host pode responder ICMP e, ainda assim, apresentar falha de SSH, HTTP ou coleta do agente.

Como melhoria futura, o ambiente pode receber HTTPS, autenticação centralizada, retenção de métricas ajustada, backups das configurações e integração com um projeto SOC/SIEM separado.

## Checklist final

- [x] Rede privada e tabela de IPs documentadas.
- [x] Três VMs instaladas e validadas.
- [x] IP, gateway, DNS e horário corretos.
- [x] SSH e HTTP funcionando.
- [x] Capturas de ICMP, ARP, DNS, TCP e TLS.
- [x] Zabbix Server e Agent 2 funcionando.
- [x] ICMP, HTTP, CPU, memória, disco e rede monitorados.
- [x] Grafana integrado ao Zabbix.
- [x] Dashboard NOC criado.
- [x] Regras de segurança revisadas.
- [x] Incidente controlado investigado e corrigido.
- [x] Nenhuma credencial real publicada.

## Estrutura deste repositório

```text
projeto-noc-modelo/
├── README.md
└── imagens/
    ├── fase01-planejamento.png
    ├── fase02-vms.png
    ├── fase03-conectividade.png
    ├── fase04-preparacao-linux.png
    ├── fase05-servicos.png
    ├── fase06-wireshark.png
    ├── fase07-zabbix-server.png
    ├── fase08-agent2.png
    ├── fase09-monitoramento.png
    ├── fase10-grafana.png
    ├── fase11-api-zabbix.png
    ├── fase12-integracao.png
    ├── fase13-dashboard.png
    ├── fase14-seguranca.png
    ├── fase15-incidentes.png
    └── fase16-evidencias.png
```
