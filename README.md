# Laboratório SOC - Resposta a Incidentes

![Splunk](https://img.shields.io/badge/Splunk-SIEM-blue) ![Status](https://img.shields.io/badge/Status-Ativo-green) ![Licença](https://img.shields.io/badge/Licença-MIT-yellow)

## O que é isso

Esse repositório documenta um laboratório de resposta a incidentes que montei do zero na minha própria máquina. O cenário simula um ataque real em duas fases: brute force seguido de execução de PowerShell malicioso.

Tudo foi construído com Splunk Enterprise rodando direto no Windows (sem VM externa, sem servidor dedicado), coletando logs de Security e PowerShell Operational.

O incidente simulado cobre:

- Tentativas de brute force contra uma conta Windows local
- Acesso bem-sucedido após várias falhas
- Download de payload via PowerShell
- Execução de comando PowerShell ofuscado em Base64
- Reconhecimento interno (whoami, ipconfig)

---

## Timeline do ataque

| Horário | O que aconteceu | Detecção |
|---------|----------------|----------|
| 12:45:00 | 8 tentativas de logon com falha em 1 minuto | DET-001 |
| 12:45:57 | Logon bem-sucedido após as falhas | DET-002 |
| 12:46:19 | PowerShell tentando baixar payload externo | DET-003 |
| 12:46:32 | Reconhecimento: whoami + ipconfig | DET-005 |
| 12:46:51 | Execução de comando PowerShell codificado em Base64 | DET-004 |

---

## As 5 detecções

Cada detecção foi mapeada para uma técnica do MITRE ATT&CK:

| ID | Nome da Detecção | Técnica MITRE ATT&CK | Severidade |
|----|-----------------|---------------------|------------|
| DET-001 | Spike de Brute Force | T1110.001 — Password Guessing | Alta |
| DET-002 | Brute Force com Sucesso | T1110.001 — Password Guessing | Crítica |
| DET-003 | Download via PowerShell | T1059.001 — PowerShell | Alta |
| DET-004 | PowerShell Ofuscado | T1027 — Obfuscated Files | Alta |
| DET-005 | Reconhecimento Interno | T1082 — System Info Discovery | Média |

Todas as queries estão na pasta detections/ com comentários explicando cada comando SPL usado.

---

## Ambiente do laboratório

- **SIEM:** Splunk Enterprise 10.2.1
- **Fontes de log:** Windows Security, System, PowerShell/Operational
- **Host simulado:** Serello (Windows 11)
- **Event IDs monitorados:** 4625 (falha de logon), 4624 (logon sucesso), 4104 (Script Block Logging do PowerShell)
- **Hardware:** 8GB RAM — tudo rodando localmente

---

## Como usar

### Importar o dashboard no Splunk

1. Abre o Splunk Web - **Dashboards** - **Create New Dashboard**
2. Escolhe **Classic Dashboard**
3. Clica em **Source** e cola o conteúdo de: dashboard/soc_incident_dashboard.xml
4. Salva e visualiza

### Rodar as detecções

1. Abre o Splunk - **Search & Reporting**
2. Copia qualquer arquivo .spl da pasta: detections/
3. Ajusta o time range se necessário (Last 4 hours funciona bem)
4. Roda a query

### Configurar o forwarder

1. Copia configs/inputs.conf para $SPLUNK_HOME/etc/system/local/
2. Reinicia o Splunk Universal Forwarder

---

## Relatório de incidente

O arquivo INCIDENT_REPORT.md documenta o ciclo completo de resposta ao incidente seguindo:

- **NIST SP 800-61** (4 fases de resposta)
- **NIST CSF 2.0** (Identify / Protect / Detect / Respond / Recover)
- **ISO 27035** (gestão de incidentes)
- **CIS Controls v8** (gaps identificados + recomendações)

Inclui:
- Sumário executivo
- Indicadores de comprometimento (IoCs)
- Timeline completa dos eventos
- Ações de contenção e erradicação
- Lições aprendidas

---

## Referências

- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [Splunk Security Essentials](https://splunkbase.splunk.com/app/3435)
- [NIST SP 800-61 — Guia de Resposta a Incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)
- [Windows Security Event IDs](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/)

---

Laboratório criado como parte do estudo para certificações Blue Team/SOC. Toda a atividade foi simulada em ambiente isolado.
