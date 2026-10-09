# Relatório Técnico de Capacitação: Operações de SOC Nível 1 (TryHackMe)

> **Fonte original:** TryHackMe, Trilha SOC Nível 1
>
> **Aviso:** este documento é um registro pessoal de estudos e não substitui os módulos e laboratórios originais da plataforma.

Este relatório consolida o conhecimento prático e teórico adquirido no treinamento de Operações de SOC Nível 1, cobrindo o ciclo de vida completo do tratamento de incidentes: monitoramento, enriquecimento de dados, triagem, relatórios, escalonamento e gestão de métricas operacionais.

---

## Índice

1. [Visão geral e arquitetura operacional do SOC](#1-visão-geral-e-arquitetura-operacional-do-soc)
2. [Vetores de ataque e superfície de exposição](#2-vetores-de-ataque-e-superfície-de-exposição)
3. [Metodologia de triagem e investigação de alertas](#3-metodologia-de-triagem-e-investigação-de-alertas)
4. [Estruturação de relatórios de alerta e escalonamento](#4-estruturação-de-relatórios-de-alerta-e-escalonamento)
5. [Consultas, inventários e playbooks](#5-consultas-inventários-e-playbooks-soc-workbooks)
6. [Métricas de desempenho (KPIs) e SLAs](#6-métricas-de-desempenho-kpis-e-acordos-de-nível-de-serviço-sla)
7. [Conclusão](#7-conclusão)

---

## 1. Visão geral e arquitetura operacional do SOC

O **Centro de Operações de Segurança (SOC)** atua na garantia da **Confidencialidade, Integridade e Disponibilidade** dos ativos de informação. As ferramentas centrais do ecossistema de monitoramento dividem-se em:

| Ferramenta | Nome completo | Função |
|---|---|---|
| **SIEM** | Security Information and Event Management | Centraliza, correlaciona e analisa eventos de segurança em tempo real. |
| **EDR/NDR** | Endpoint/Network Detection and Response | Fornece visibilidade detalhada de processos nos endpoints e no tráfego de rede. |
| **SOAR** | Security Orchestration, Automation, and Response | Automatiza respostas a incidentes e fluxos repetitivos. |
| **ITSM** | IT Service Management | Gerencia chamados, atribuição de responsáveis e acompanhamento de SLA. |

---

## 2. Vetores de ataque e superfície de exposição (uma breve revisão do conteúdo Apresentando a Equipe Azul)

A triagem inicial exige a compreensão dos principais pontos de entrada utilizados por agentes maliciosos:

- **Vulnerabilidades conhecidas (CVEs):** exploração de brechas em softwares não atualizados.
- **Erros de configuração (*misconfigurations*):** serviços expostos indevidamente à internet sem autenticação adequada.
- **Ataques à cadeia de suprimentos (*supply chain*):** comprometimento de softwares de terceiros para infiltrar redes corporativas.
- **Hardening insuficiente:** falta de restrições de privilégio, ausência de MFA e configurações frouxas em diretórios de identidade.

---

## 3. Metodologia de triagem e investigação de alertas

A triagem efetuada pelo analista de Nível 1 (L1) visa separar o ruído das ameaças reais:

- **Atribuição:** assumir a propriedade do alerta não atribuído e alterar o status para *Em Andamento* (*In Progress*).
- **Análise contextual vs. dados brutos:** avaliar o volume e o padrão de tráfego. Por exemplo, volume elevado em salas de reunião direcionado ao Zoom é classificado como **falso positivo**, enquanto conexões suspeitas seguidas de varreduras internas são **verdadeiro positivo**.
- **Determinação do veredito:** classificação entre **True Positive (TP)** ou **False Positive (FP)**.

---

## 4. Estruturação de relatórios de alerta e escalonamento

### 4.1 Metodologia dos 5 Ws para relatórios

Para garantir que o histórico seja mantido além da retenção de logs brutos do SIEM (que varia de 3 a 12 meses), o relatório do analista deve conter:

| Elemento | Pergunta-chave | Exemplo prático |
|---|---|---|
| **Quem (Who)** | Qual conta ou usuário executou a ação? | `m.boslan@tryhackme.thm` |
| **O quê (What)** | Qual foi a sequência de ações observada? | Download de documento confidencial / phishing de `support@microsoft.com` |
| **Quando (When)** | Quando a atividade iniciou e terminou? | Timestamps exatos de início e término do evento |
| **Onde (Where)** | Qual host, IP ou serviço está envolvido? | Dispositivo local ou IP de origem/destino |
| **Por quê (Why)** | Qual a razão técnica para o veredito? | Análise comportamental do payload ou regra violada |

### 4.2 Fluxo de escalonamento e comunicação

- **Escalonamento:** quando o evento exige contenção (isolamento de host, reset de senha) ou investigação aprofundada por DFIR, atribui-se o caso ao analista L2 de plantão (ex.: E.Fleming).
- **Comunicação out-of-band:** em caso de comprometimento de credenciais de chat/e-mail (Slack/Teams), contata-se o usuário por um meio alternativo (ex.: telefone) para não alertar o atacante.
- **Canais de emergência:** em incidentes críticos, notifica-se o L2 de plantão antes de acionar a gerência.

---

## 5. Consultas, inventários e playbooks (SOC Workbooks)

### 5.1 Enriquecimento de contexto (*enrichment*)

A fase de enriquecimento coleta contexto prévio para embasar a investigação:

- **Inventário de identidades:** mapeia usuários, cargos e privilégios via Active Directory, Okta, Google Workspace e sistemas de RH (como BambooHR).
- **Inventário de ativos:** mapeia hosts, servidores e criticidade via EDR (Elastic, CrowdStrike) e MDM (Intune, Jamf).
- **Diagramas de rede:** permitem rastrear a tradução de IPs (NAT em VPNs) e a movimentação lateral entre sub-redes (ex.: sub-rede de banco de dados vs. sub-rede de escritório).

### 5.2 Estrutura de um playbook de SOC

Os playbooks (SOC Workbooks) padronizam a resposta a incidentes em três etapas centrais:

```text
[1. Enriquecimento] --> Coleta de identidade, cargo e inventário de ativos
        |
        v
[2. Investigação]   --> Análise de logs no SIEM/EDR e verificação de comportamento
        |
        v
[3. Escalonamento]  --> Notificação ao L2/TI ou execução de medidas de contenção
```

---

## 6. Métricas de desempenho (KPIs) e Acordos de Nível de Serviço (SLA)

### 6.1 Indicadores-chave de desempenho (KPIs)

- **Contagem de Alertas (AC):** carga total de alertas. O volume ideal por analista L1 é de **5 a 30 alertas/dia**.
- **Taxa de Falsos Positivos (FPR):** valores acima de **80%** exigem ajuste fino nas regras de detecção ou automação.

  ```text
  FPR = (Falsos Positivos / Total de Alertas) x 100
  ```

- **Taxa de Escalonamento de Alertas (AER):** o objetivo operacional é manter a taxa abaixo de **50%** (idealmente abaixo de 20%).

  ```text
  AER = (Alertas Escalonados / Total de Alertas) x 100
  ```

- **Taxa de Detecção de Ameaças (TDR):** deve ser mantida obrigatoriamente em **100%**.

  ```text
  TDR = (Ameaças Detectadas / Total de Ameaças Reais) x 100
  ```

### 6.2 Métricas de tempo (SLA)

| Métrica | Nome | SLA alvo | Descrição |
|---|---|---|---|
| **MTTD** | Mean Time to Detect | ≤ 5 min | Tempo entre a ação maliciosa e a geração do alerta. |
| **MTTA** | Mean Time to Acknowledge | ≤ 10 min | Tempo até o analista L1 assumir a triagem do alerta. |
| **MTTR** | Mean Time to Respond | ≤ 60 min | Tempo total para conter ou remediar a ameaça. |

---

## 7. Conclusão

A capacitação cobriu os requisitos operacionais para a função de Analista de Segurança SOC Nível 1, englobando desde o manuseio de ferramentas de visibilidade até a condução rigorosa de relatórios e o controle de SLAs. Este documento serve como referência técnica para documentação de repositórios e práticas operacionais em ambientes SOC.
